---
title: Fehlerbehandlung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: e65983fb1a440057283111ea5a79a79b765607b7
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751681"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="bdf34-103">Fehlerbehandlung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bdf34-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bdf34-104">Von [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bdf34-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bdf34-105">Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="bdf34-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="bdf34-106">Weitere Informationen für Web-Apps finden Sie unter <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="bdf34-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="bdf34-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="bdf34-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="bdf34-108">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample).) Die Registerkarte „Netzwerk“ in den F12-Entwicklertools im Browser ist beim Testen der Beispiel-App nützlich.</span><span class="sxs-lookup"><span data-stu-id="bdf34-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="bdf34-109">Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="bdf34-109">Developer Exception Page</span></span>

<span data-ttu-id="bdf34-110">Die *Seite mit Ausnahmen für Entwickler* enthält ausführliche Informationen zu Anforderungsausnahmen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="bdf34-111">Die ASP.NET Core-Vorlagen generieren den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bdf34-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="bdf34-112">Der vorangehende hervorgehobene Code aktiviert die Seite mit Ausnahmen für Entwickler, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bdf34-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="bdf34-113">Die Vorlagen platzieren <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> am Anfang der Middlewarepipeline, sodass die in der nachfolgenden Middleware ausgelösten Ausnahmen abgefangen werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="bdf34-114">Der vorangehende Code aktiviert die Seite mit Ausnahmen für Entwickler **nur** dann, wenn die App in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bdf34-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="bdf34-115">Bei Ausführung der App in der Produktionsumgebung sollten keine detaillierten Informationen zu den Ausnahmen öffentlich angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="bdf34-116">Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="bdf34-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bdf34-117">Die Seite mit Ausnahmen für Entwickler enthält die folgenden Informationen zu der Ausnahme und der Anforderung:</span><span class="sxs-lookup"><span data-stu-id="bdf34-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="bdf34-118">Stapelüberwachung</span><span class="sxs-lookup"><span data-stu-id="bdf34-118">_ Stack trace</span></span>
* <span data-ttu-id="bdf34-119">Abfragezeichenfolgeparameter, sofern vorhanden</span><span class="sxs-lookup"><span data-stu-id="bdf34-119">Query string parameters if any</span></span>
* <span data-ttu-id="bdf34-120">Cookies, sofern vorhanden</span><span class="sxs-lookup"><span data-stu-id="bdf34-120">Cookies if any</span></span>
* <span data-ttu-id="bdf34-121">Header</span><span class="sxs-lookup"><span data-stu-id="bdf34-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="bdf34-122">Seite „Ausnahmehandler“</span><span class="sxs-lookup"><span data-stu-id="bdf34-122">Exception handler page</span></span>

<span data-ttu-id="bdf34-123">Um eine benutzerdefinierte Fehlerbehandlungsseite für die [Produktionsumgebung](xref:fundamentals/environments) zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> auf.</span><span class="sxs-lookup"><span data-stu-id="bdf34-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="bdf34-124">Die Aufgaben der Middleware zur Ausnahmebehandlung:</span><span class="sxs-lookup"><span data-stu-id="bdf34-124">This exception handling middleware:</span></span>

* <span data-ttu-id="bdf34-125">Dient zum Abfangen und Protokollieren von Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="bdf34-126">Führt erneut die Anforderung in einer anderen Pipeline im angegebenen Pfad aus.</span><span class="sxs-lookup"><span data-stu-id="bdf34-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="bdf34-127">Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="bdf34-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="bdf34-128">Der von der Vorlage generierte Code führt die Anforderung erneut mit dem `/Error`-Pfad aus.</span><span class="sxs-lookup"><span data-stu-id="bdf34-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="bdf34-129">Im folgenden Beispiel fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu:</span><span class="sxs-lookup"><span data-stu-id="bdf34-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="bdf34-130">Die Razor Pages-App-Vorlage stellt im Ordner *Pages* eine Fehlerseite ( *.cshtml*) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) bereit.</span><span class="sxs-lookup"><span data-stu-id="bdf34-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="bdf34-131">Die Projektvorlage einer MVC-App enthält die Aktionsmethode `Error` und die Ansicht „Error“ im Home-Controller.</span><span class="sxs-lookup"><span data-stu-id="bdf34-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="bdf34-132">Die Middleware für die Ausnahmebehandlung führt die Anforderung unter Verwendung der *ursprünglichen* HTTP-Methode erneut aus.</span><span class="sxs-lookup"><span data-stu-id="bdf34-132">The exception handling middleware re-executes the request using the *original* HTTP method.</span></span> <span data-ttu-id="bdf34-133">Wenn ein Endpunkt für die Fehlerbehandlung auf bestimmte HTTP-Methoden beschränkt ist, wird er nur für diese Methoden ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-133">If an error handler endpoint is restricted to a specific set of HTTP methods, it runs only for those HTTP methods.</span></span> <span data-ttu-id="bdf34-134">Beispielsweise wird eine MVC-Controller-Aktion, die das Attribut `[HttpGet]` verwendet, nur für GET-Anforderungen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-134">For example, an MVC controller action that uses the `[HttpGet]` attribute runs only for GET requests.</span></span> <span data-ttu-id="bdf34-135">Um sicherzustellen, dass *alle* Anforderungen die benutzerdefinierte Seite für die Fehlerbehandlung erreichen, beschränken Sie die Endpunkte nicht auf bestimmte HTTP-Methoden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-135">To ensure that *all* requests reach the custom error handling page, don't restrict them to a specific set of HTTP methods.</span></span>

<span data-ttu-id="bdf34-136">So behandeln Sie Ausnahmen unterschiedlich, basierend auf der ursprünglichen HTTP-Methode:</span><span class="sxs-lookup"><span data-stu-id="bdf34-136">To handle exceptions differently based on the original HTTP method:</span></span>

* <span data-ttu-id="bdf34-137">Für Razor Pages erstellen Sie mehrere Handlermethoden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-137">For Razor Pages, create multiple handler methods.</span></span> <span data-ttu-id="bdf34-138">Verwenden Sie beispielsweise `OnGet` zum Behandeln von GET-Ausnahmen und `OnPost` zum Behandeln von POST-Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-138">For example, use `OnGet` to handle GET exceptions and use `OnPost` to handle POST exceptions.</span></span>
* <span data-ttu-id="bdf34-139">Für MVC wenden Sie HTTP-Verbattribute auf mehrere Aktionen an.</span><span class="sxs-lookup"><span data-stu-id="bdf34-139">For MVC, apply HTTP verb attributes to multiple actions.</span></span> <span data-ttu-id="bdf34-140">Verwenden Sie beispielsweise `[HttpGet]` zum Behandeln von GET-Ausnahmen und `[HttpPost]` zum Behandeln von POST-Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-140">For example, use `[HttpGet]` to handle GET exceptions and use `[HttpPost]` to handle POST exceptions.</span></span>

<span data-ttu-id="bdf34-141">Um nicht authentifizierten Benutzern die Anzeige der benutzerdefinierten Seite für die Fehlerbehandlung zu ermöglichen, stellen Sie sicher, dass der anonyme Zugriff unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="bdf34-141">To allow unauthenticated users to view the custom error handling page, ensure that it supports anonymous access.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="bdf34-142">Zugreifen auf die Ausnahme</span><span class="sxs-lookup"><span data-stu-id="bdf34-142">Access the exception</span></span>

<span data-ttu-id="bdf34-143">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Fehlerhandler zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-143">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="bdf34-144">Der folgende Code fügt dem Standard *Pages/Error.cshtml.cs* die von den ASP.NET Core-Vorlagen generierte `ExceptionMessage` hinzu:</span><span class="sxs-lookup"><span data-stu-id="bdf34-144">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="bdf34-145">Stellen Sie Clients **keine** vertraulichen Fehlerinformationen bereit.</span><span class="sxs-lookup"><span data-stu-id="bdf34-145">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="bdf34-146">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="bdf34-146">Serving errors is a security risk.</span></span>

<span data-ttu-id="bdf34-147">So testen Sie die Ausnahme in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)</span><span class="sxs-lookup"><span data-stu-id="bdf34-147">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="bdf34-148">Legen Sie die Umgebung auf Produktion fest.</span><span class="sxs-lookup"><span data-stu-id="bdf34-148">Set the environment to production.</span></span>
* <span data-ttu-id="bdf34-149">Entfernen Sie die Kommentare aus `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bdf34-149">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="bdf34-150">Wählen Sie **Trigger an exception** (Ausnahme auslösen) auf der Startseite aus.</span><span class="sxs-lookup"><span data-stu-id="bdf34-150">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="bdf34-151">Lambda-Ausdruck für Ausnahmehandler</span><span class="sxs-lookup"><span data-stu-id="bdf34-151">Exception handler lambda</span></span>

<span data-ttu-id="bdf34-152">Eine Alternative zu einer [benutzerdefinierten Ausnahmebehandlungsseite](#exception-handler-page) ist das Angeben eines Lambda-Ausdrucks für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="bdf34-152">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="bdf34-153">Die Verwendung eines Lambda-Ausdrucks ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="bdf34-153">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="bdf34-154">Der folgende Code verwendet einen Lambdaausdruck für die Ausnahmebehandlung:</span><span class="sxs-lookup"><span data-stu-id="bdf34-154">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="bdf34-155">Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit.</span><span class="sxs-lookup"><span data-stu-id="bdf34-155">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="bdf34-156">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="bdf34-156">Serving errors is a security risk.</span></span>

<span data-ttu-id="bdf34-157">So testen Sie den Lambdaausdruck für die Ausnahmebehandlung in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)</span><span class="sxs-lookup"><span data-stu-id="bdf34-157">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="bdf34-158">Legen Sie die Umgebung auf Produktion fest.</span><span class="sxs-lookup"><span data-stu-id="bdf34-158">Set the environment to production.</span></span>
* <span data-ttu-id="bdf34-159">Entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bdf34-159">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="bdf34-160">Wählen Sie **Trigger an exception** (Ausnahme auslösen) auf der Startseite aus.</span><span class="sxs-lookup"><span data-stu-id="bdf34-160">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="bdf34-161">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="bdf34-161">UseStatusCodePages</span></span>

<span data-ttu-id="bdf34-162">Die ASP.NET Core-App stellt für HTTP-Fehlerstatuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="bdf34-162">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="bdf34-163">Wenn die App einen HTTP-Fehlerstatuscode 400–599 feststellt, der keinen Text enthält, werden der Statuscode und ein leerer Antworttext zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="bdf34-163">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="bdf34-164">Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-164">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="bdf34-165">Um für gängige Statuscodes einfache Handler im Textformat zu aktivieren, rufen Sie in der `Startup.Configure`-Methode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> auf:</span><span class="sxs-lookup"><span data-stu-id="bdf34-165">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="bdf34-166">Rufen Sie `UseStatusCodePages` vor der Middleware für die Anforderungsverarbeitung auf.</span><span class="sxs-lookup"><span data-stu-id="bdf34-166">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="bdf34-167">Rufen Sie beispielsweise `UseStatusCodePages` vor der Middleware für statische Dateien und der Middleware für Endpunkte auf.</span><span class="sxs-lookup"><span data-stu-id="bdf34-167">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="bdf34-168">Wenn `UseStatusCodePages` nicht verwendet wird, wird beim Navigieren zu einer URL ohne Endpunkt eine browserabhängige Fehlermeldung zurückgegeben, die angibt, dass der Endpunkt nicht gefunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="bdf34-168">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="bdf34-169">Navigieren Sie zum Beispiel zu `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="bdf34-169">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="bdf34-170">Wenn `UseStatusCodePages` aufgerufen wird, gibt der Browser Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="bdf34-170">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="bdf34-171">`UseStatusCodePages` wird normalerweise nicht in der Produktionsumgebung verwendet, da diese Methode eine Meldung zurückgibt, die für Benutzer nicht nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="bdf34-171">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="bdf34-172">So testen Sie `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)</span><span class="sxs-lookup"><span data-stu-id="bdf34-172">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="bdf34-173">Legen Sie die Umgebung auf Produktion fest.</span><span class="sxs-lookup"><span data-stu-id="bdf34-173">Set the environment to production.</span></span>
* <span data-ttu-id="bdf34-174">Entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bdf34-174">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="bdf34-175">Klicken Sie auf die Links auf der Startseite.</span><span class="sxs-lookup"><span data-stu-id="bdf34-175">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="bdf34-176">Die Middleware für Statuscodeseiten erfasst **keine** Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-176">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="bdf34-177">Um eine benutzerdefinierte Fehlerbehandlungsseite bereitzustellen, verwenden Sie die [Ausnahmehandlerseite](#exception-handler-page).</span><span class="sxs-lookup"><span data-stu-id="bdf34-177">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="bdf34-178">UseStatusCodePages mit Formatzeichenfolge</span><span class="sxs-lookup"><span data-stu-id="bdf34-178">UseStatusCodePages with format string</span></span>

<span data-ttu-id="bdf34-179">Um den Inhaltstyp und Text der Antwort anzupassen, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Inhaltstyp und eine Formatierungszeichenfolge erfordert:</span><span class="sxs-lookup"><span data-stu-id="bdf34-179">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="bdf34-180">Im vorangehenden Code ist `{0}` ein Platzhalter für den Fehlercode.</span><span class="sxs-lookup"><span data-stu-id="bdf34-180">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="bdf34-181">`UseStatusCodePages` mit Formatzeichenfolge wird normalerweise nicht in der Produktionsumgebung verwendet, da diese Methode eine Meldung zurückgibt, die für Benutzer nicht nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="bdf34-181">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="bdf34-182">Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bdf34-182">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="bdf34-183">UseStatusCodePages mit Lambda-Ausdruck</span><span class="sxs-lookup"><span data-stu-id="bdf34-183">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="bdf34-184">Um benutzerdefinierten Code für die Fehlerbehandlung und das Schreiben von Antworten anzugeben, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Lambda-Ausdruck verwendet:</span><span class="sxs-lookup"><span data-stu-id="bdf34-184">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="bdf34-185">`UseStatusCodePages` mit Lambdaausdruck wird normalerweise nicht in der Produktionsumgebung verwendet, da diese Methode eine Meldung zurückgibt, die für Benutzer nicht nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="bdf34-185">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="bdf34-186">Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bdf34-186">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="bdf34-187">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="bdf34-187">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="bdf34-188">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="bdf34-188">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="bdf34-189">Sendet den Statuscode [302 Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (Gefunden) an den Client.</span><span class="sxs-lookup"><span data-stu-id="bdf34-189">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="bdf34-190">Leitet den Client an den in der URL-Vorlage angegebenen Fehlerbehandlungsendpunkt weiter.</span><span class="sxs-lookup"><span data-stu-id="bdf34-190">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="bdf34-191">Der Fehlerbehandlungsendpunkt zeigt in der Regel Fehlerinformationen an und gibt den HTTP-Code 200 zurück.</span><span class="sxs-lookup"><span data-stu-id="bdf34-191">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="bdf34-192">Die URL-Vorlage kann, wie im vorangehenden Code gezeigt, einen `{0}`-Platzhalter für den Statuscode enthalten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-192">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="bdf34-193">Wenn die URL-Vorlage mit einer Tilde (`~`) beginnt, wird `~` durch die Angabe für `PathBase` der App ersetzt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-193">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="bdf34-194">Wenn Sie in der App einen Endpunkt angeben, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-194">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bdf34-195">Ein Razor Pages-Beispiel finden Sie unter [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="bdf34-195">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="bdf34-196">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="bdf34-196">This method is commonly used when the app:</span></span>

* <span data-ttu-id="bdf34-197">Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="bdf34-197">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="bdf34-198">Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="bdf34-198">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="bdf34-199">Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="bdf34-199">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="bdf34-200">Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bdf34-200">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="bdf34-201">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="bdf34-201">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="bdf34-202">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="bdf34-202">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="bdf34-203">Gibt den ursprünglichen Statuscode an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="bdf34-203">Returns the original status code to the client.</span></span>
* <span data-ttu-id="bdf34-204">Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.</span><span class="sxs-lookup"><span data-stu-id="bdf34-204">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="bdf34-205">Wenn in der App ein Endpunkt angegeben ist, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-205">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bdf34-206">Stellen Sie sicher, dass `UseStatusCodePagesWithReExecute` vor `UseRouting` platziert wird, damit die Anforderung an die Statusseite umgeleitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="bdf34-206">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="bdf34-207">Ein Razor Pages-Beispiel finden Sie unter [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="bdf34-207">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="bdf34-208">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="bdf34-208">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="bdf34-209">Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-209">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="bdf34-210">Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="bdf34-210">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="bdf34-211">Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="bdf34-211">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="bdf34-212">Die Vorlagen für URL und Abfragezeichenfolgen können für den Statuscode einen Platzhalter (`{0}`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-212">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="bdf34-213">Die URL-Vorlage muss mit einem Schrägstrich (`/`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-213">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="bdf34-214">Der Endpunkt, der den Fehler verarbeitet, kann die ursprüngliche URL abrufen, die den Fehler generiert hat (siehe folgendes Beispiel):</span><span class="sxs-lookup"><span data-stu-id="bdf34-214">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="bdf34-215">Ein Razor Pages-Beispiel finden Sie unter [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="bdf34-215">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="bdf34-216">Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="bdf34-216">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="bdf34-217">Deaktivieren von Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="bdf34-217">Disable status code pages</span></span>

<span data-ttu-id="bdf34-218">Verwenden Sie das Attribut [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute), um Statuscodeseiten für einen MVC-Controller oder eine MVC-Aktionsmethode zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="bdf34-218">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="bdf34-219">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>, um Statuscodeseiten für bestimmte Anforderungen in der Handlermethode von Razor Pages oder in einem MVC-Controller zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="bdf34-219">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="bdf34-220">Ausnahmebehandlungscode</span><span class="sxs-lookup"><span data-stu-id="bdf34-220">Exception-handling code</span></span>

<span data-ttu-id="bdf34-221">Code auf Seiten zur Ausnahmebehandlung kann ebenfalls Ausnahmen auslösen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-221">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="bdf34-222">Produktionsfehlerseiten sollten gründlich getestet werden. Achten Sie darauf, dabei keine eigenen Ausnahmen auszulösen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-222">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="bdf34-223">Antwortheader</span><span class="sxs-lookup"><span data-stu-id="bdf34-223">Response headers</span></span>

<span data-ttu-id="bdf34-224">Nachdem die Header für eine Antwort gesendet wurden:</span><span class="sxs-lookup"><span data-stu-id="bdf34-224">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="bdf34-225">Die App kann den Statuscode der Antwort nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="bdf34-225">The app can't change the response's status code.</span></span>
* <span data-ttu-id="bdf34-226">Weder Ausnahmeseiten noch Handler können ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-226">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="bdf34-227">Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-227">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="bdf34-228">Sichere Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="bdf34-228">Server exception handling</span></span>

<span data-ttu-id="bdf34-229">Neben der Ausnahmebehandlungslogik in einer App kann die [HTTP-Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-229">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="bdf34-230">Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort `500 - Internal Server Error` ohne Antworttext.</span><span class="sxs-lookup"><span data-stu-id="bdf34-230">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="bdf34-231">Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="bdf34-231">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="bdf34-232">Anforderungen, die nicht von der App verarbeitet werden, werden vom Server verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="bdf34-232">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="bdf34-233">Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="bdf34-233">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="bdf34-234">Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-234">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="bdf34-235">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="bdf34-235">Startup exception handling</span></span>

<span data-ttu-id="bdf34-236">Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-236">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="bdf34-237">Der Host kann für das [Erfassen von Startfehlern](xref:fundamentals/host/web-host#capture-startup-errors) und [Erfassen detaillierter Fehler](xref:fundamentals/host/web-host#detailed-errors) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-237">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="bdf34-238">Die Hostingebene kann nur dann für einen beim Start erfassten Fehler eine Fehlerseite anzeigen, wenn der Fehler nach der Bindung an die Hostadresse bzw. den Port auftritt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-238">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="bdf34-239">Wenn die Bindung misslingt:</span><span class="sxs-lookup"><span data-stu-id="bdf34-239">If binding fails:</span></span>

* <span data-ttu-id="bdf34-240">Die Hostebene protokolliert eine kritische Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="bdf34-240">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="bdf34-241">Der DotNet-Prozess stürzt ab.</span><span class="sxs-lookup"><span data-stu-id="bdf34-241">The dotnet process crashes.</span></span>
* <span data-ttu-id="bdf34-242">Wenn der HTTP-Server [Kestrel](xref:fundamentals/servers/kestrel) heißt, wird keine Fehlerseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-242">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="bdf34-243">Wenn sie auf [IIS](/iis) (oder Azure App Service) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann.</span><span class="sxs-lookup"><span data-stu-id="bdf34-243">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="bdf34-244">Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="bdf34-244">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="bdf34-245">Datenbank-Fehlerseite</span><span class="sxs-lookup"><span data-stu-id="bdf34-245">Database error page</span></span>

<span data-ttu-id="bdf34-246">Der Ausnahmefilter der Datenbankentwicklerseite `AddDatabaseDeveloperPageExceptionFilter` erfasst datenbankbezogene Ausnahmen, die mithilfe von Entity Framework Core-Migrationen aufgelöst werden können.</span><span class="sxs-lookup"><span data-stu-id="bdf34-246">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="bdf34-247">Wenn diese Ausnahmen auftreten, wird eine HTML-Antwort mit Details zu möglichen Aktionen zum Beheben des Problems generiert.</span><span class="sxs-lookup"><span data-stu-id="bdf34-247">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="bdf34-248">Diese Seite ist nur in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="bdf34-248">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="bdf34-249">Der folgende Code wurde von den ASP.NET Core Razor Pages-Vorlagen generiert, als einzelne Benutzerkonten angegeben wurden:</span><span class="sxs-lookup"><span data-stu-id="bdf34-249">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="bdf34-250">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="bdf34-250">Exception filters</span></span>

<span data-ttu-id="bdf34-251">In MVC-Apps können Ausnahmefilter global oder auf einen Controller oder eine Aktion bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-251">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="bdf34-252">In Razor Pages-Apps können sie global oder auf ein Seitenmodell bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-252">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="bdf34-253">Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-253">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="bdf34-254">Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="bdf34-254">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="bdf34-255">Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen ausgelöst werden. Sie sind jedoch nicht so flexibel wie die integrierte [Middleware für die Ausnahmebehandlung](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span><span class="sxs-lookup"><span data-stu-id="bdf34-255">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="bdf34-256">Es wird empfohlen, `UseExceptionHandler` zu verwenden, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine andere Strategie auswählen müssen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-256">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="bdf34-257">Modellstatusfehler</span><span class="sxs-lookup"><span data-stu-id="bdf34-257">Model state errors</span></span>

<span data-ttu-id="bdf34-258">Informationen zum Behandeln von Modellstatusfehlern finden Sie unter [Modellbindung](xref:mvc/models/model-binding) und [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="bdf34-258">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdf34-259">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bdf34-259">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="bdf34-260">Von [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bdf34-260">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bdf34-261">Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="bdf34-261">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="bdf34-262">Weitere Informationen für Web-Apps finden Sie unter <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="bdf34-262">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="bdf34-263">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="bdf34-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="bdf34-264">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="bdf34-264">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="bdf34-265">Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="bdf34-265">Developer Exception Page</span></span>

<span data-ttu-id="bdf34-266">Die *Seite mit Ausnahmen für Entwickler* enthält ausführliche Informationen zu Anforderungsausnahmen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-266">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="bdf34-267">Die ASP.NET Core-Vorlagen generieren den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bdf34-267">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="bdf34-268">Der vorangehende Code aktiviert die Seite mit Ausnahmen für Entwickler, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bdf34-268">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="bdf34-269">Die Vorlagen platzieren <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> vor jeder Middleware, sodass Ausnahmen in der nachfolgenden Middleware abgefangen werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-269">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="bdf34-270">Der vorangehende Code aktiviert die Seite mit Ausnahmen für Entwickler nur dann, wenn die App in der **Entwicklungsumgebung** ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bdf34-270">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="bdf34-271">Bei Ausführung der App in der Produktionsumgebung sollten keine detaillierten Informationen zu den Ausnahmen öffentlich angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-271">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="bdf34-272">Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="bdf34-272">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bdf34-273">Die Seite mit Ausnahmen für Entwickler enthält die folgenden Informationen zu der Ausnahme und der Anforderung:</span><span class="sxs-lookup"><span data-stu-id="bdf34-273">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="bdf34-274">Stapelüberwachung</span><span class="sxs-lookup"><span data-stu-id="bdf34-274">Stack trace</span></span>
* <span data-ttu-id="bdf34-275">Abfragezeichenfolgeparameter, sofern vorhanden</span><span class="sxs-lookup"><span data-stu-id="bdf34-275">Query string parameters if any</span></span>
* <span data-ttu-id="bdf34-276">Cookies, sofern vorhanden</span><span class="sxs-lookup"><span data-stu-id="bdf34-276">Cookies if any</span></span>
* <span data-ttu-id="bdf34-277">Header</span><span class="sxs-lookup"><span data-stu-id="bdf34-277">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="bdf34-278">Seite „Ausnahmehandler“</span><span class="sxs-lookup"><span data-stu-id="bdf34-278">Exception handler page</span></span>

<span data-ttu-id="bdf34-279">Um eine benutzerdefinierte Fehlerbehandlung für die Produktionsumgebung zu konfigurieren, verwenden Sie Middleware zur Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="bdf34-279">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="bdf34-280">Die Middleware:</span><span class="sxs-lookup"><span data-stu-id="bdf34-280">The middleware:</span></span>

* <span data-ttu-id="bdf34-281">Dient zum Abfangen und Protokollieren von Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-281">Catches and logs exceptions.</span></span>
* <span data-ttu-id="bdf34-282">Führt die Anforderung für die angegebene Seite oder den Controller in einer anderen Pipeline erneut aus.</span><span class="sxs-lookup"><span data-stu-id="bdf34-282">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="bdf34-283">Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="bdf34-283">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="bdf34-284">Der von der Vorlage generierte Code führt die Anforderung erneut mit `/Error` aus.</span><span class="sxs-lookup"><span data-stu-id="bdf34-284">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="bdf34-285">Im folgenden Beispiel fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu:</span><span class="sxs-lookup"><span data-stu-id="bdf34-285">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="bdf34-286">Die Razor Pages-App-Vorlage stellt im Ordner *Pages* eine Fehlerseite ( *.cshtml*) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) bereit.</span><span class="sxs-lookup"><span data-stu-id="bdf34-286">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="bdf34-287">Die Projektvorlage einer MVC-App enthält eine Fehleraktionsmethode und die Ansicht „Fehler“ im Home-Controller.</span><span class="sxs-lookup"><span data-stu-id="bdf34-287">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="bdf34-288">Markieren Sie die Aktionsmethode für die Fehlerbehandlung nicht mit HTTP-Methodenattributen wie `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="bdf34-288">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="bdf34-289">Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-289">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="bdf34-290">Lassen Sie den anonymen Zugriff auf die Methode zu, wenn nicht authentifizierten Benutzern die Fehleransicht angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="bdf34-290">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="bdf34-291">Zugreifen auf die Ausnahme</span><span class="sxs-lookup"><span data-stu-id="bdf34-291">Access the exception</span></span>

<span data-ttu-id="bdf34-292">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Controller für die Fehlerbehandlung oder auf einer Seite zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="bdf34-292">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="bdf34-293">Stellen Sie Clients **keine** vertraulichen Fehlerinformationen bereit.</span><span class="sxs-lookup"><span data-stu-id="bdf34-293">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="bdf34-294">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="bdf34-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="bdf34-295">Legen Sie die Umgebung auf Produktionen fest, und erzwingen Sie eine Ausnahme, um die vorherige Seite zur Ausnahmebehandlung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="bdf34-295">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="bdf34-296">Lambda-Ausdruck für Ausnahmehandler</span><span class="sxs-lookup"><span data-stu-id="bdf34-296">Exception handler lambda</span></span>

<span data-ttu-id="bdf34-297">Eine Alternative zu einer [benutzerdefinierten Ausnahmebehandlungsseite](#exception-handler-page) ist das Angeben eines Lambda-Ausdrucks für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="bdf34-297">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="bdf34-298">Die Verwendung eines Lambda-Ausdrucks ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="bdf34-298">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="bdf34-299">Hier ist ein Beispiel der Verwendung eines Lambda-Ausdrucks für die Ausnahmebehandlung:</span><span class="sxs-lookup"><span data-stu-id="bdf34-299">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="bdf34-300">Im vorangehenden Code wird `await context.Response.WriteAsync(new string(' ', 512));` hinzugefügt, sodass der Internet Explorer-Browser die Fehlermeldung anstelle einer IE-Fehlermeldung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-300">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="bdf34-301">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="bdf34-301">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="bdf34-302">Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit.</span><span class="sxs-lookup"><span data-stu-id="bdf34-302">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="bdf34-303">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="bdf34-303">Serving errors is a security risk.</span></span>

<span data-ttu-id="bdf34-304">Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) das Ergebnis des Lambda-Ausdrucks für die Ausnahmebehandlung anzuzeigen, verwenden Sie die Präprozessordirektiven `ProdEnvironment` und `ErrorHandlerLambda`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="bdf34-304">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="bdf34-305">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="bdf34-305">UseStatusCodePages</span></span>

<span data-ttu-id="bdf34-306">Ihre ASP.NET Core-App stellt für HTTP-Statuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="bdf34-306">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="bdf34-307">Die App gibt einen Statuscode und einen leeren Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="bdf34-307">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="bdf34-308">Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-308">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="bdf34-309">Die Middleware wird vom Paket [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-309">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="bdf34-310">Um für gängige Statuscodes einfache Handler im Textformat zu aktivieren, rufen Sie in der `Startup.Configure`-Methode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> auf:</span><span class="sxs-lookup"><span data-stu-id="bdf34-310">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="bdf34-311">Rufen Sie `UseStatusCodePages` vor Middleware zur Anforderungsverarbeitung auf (z.B. Middleware für statische Dateien und MVC-Middleware).</span><span class="sxs-lookup"><span data-stu-id="bdf34-311">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="bdf34-312">Wenn `UseStatusCodePages` nicht verwendet wird, wird beim Navigieren zu einer URL ohne Endpunkt eine browserabhängige Fehlermeldung zurückgegeben, die angibt, dass der Endpunkt nicht gefunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="bdf34-312">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="bdf34-313">Navigieren Sie zum Beispiel zu `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="bdf34-313">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="bdf34-314">Wenn `UseStatusCodePages` aufgerufen wird, gibt der Browser Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="bdf34-314">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="bdf34-315">UseStatusCodePages mit Formatzeichenfolge</span><span class="sxs-lookup"><span data-stu-id="bdf34-315">UseStatusCodePages with format string</span></span>

<span data-ttu-id="bdf34-316">Um den Inhaltstyp und Text der Antwort anzupassen, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Inhaltstyp und eine Formatierungszeichenfolge erfordert:</span><span class="sxs-lookup"><span data-stu-id="bdf34-316">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="bdf34-317">UseStatusCodePages mit Lambda-Ausdruck</span><span class="sxs-lookup"><span data-stu-id="bdf34-317">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="bdf34-318">Um benutzerdefinierten Code für die Fehlerbehandlung und das Schreiben von Antworten anzugeben, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Lambda-Ausdruck verwendet:</span><span class="sxs-lookup"><span data-stu-id="bdf34-318">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="bdf34-319">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="bdf34-319">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="bdf34-320">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="bdf34-320">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="bdf34-321">Sendet den Statuscode *302 Found* (Gefunden) an den Client.</span><span class="sxs-lookup"><span data-stu-id="bdf34-321">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="bdf34-322">Der Client wird an den in der URL-Vorlage angegebenen Standort umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="bdf34-322">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="bdf34-323">Die URL-Vorlage kann, wie im Beispiel gezeigt, einen `{0}`-Platzhalter für den Statuscode enthalten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-323">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="bdf34-324">Wenn die URL-Vorlage mit einer Tilde (`~`) beginnt, wird `~` durch die Angabe für `PathBase` der App ersetzt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-324">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="bdf34-325">Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-325">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bdf34-326">Ein Razor Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="bdf34-326">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="bdf34-327">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="bdf34-327">This method is commonly used when the app:</span></span>

* <span data-ttu-id="bdf34-328">Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="bdf34-328">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="bdf34-329">Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="bdf34-329">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="bdf34-330">Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="bdf34-330">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="bdf34-331">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="bdf34-331">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="bdf34-332">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="bdf34-332">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="bdf34-333">Gibt den ursprünglichen Statuscode an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="bdf34-333">Returns the original status code to the client.</span></span>
* <span data-ttu-id="bdf34-334">Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.</span><span class="sxs-lookup"><span data-stu-id="bdf34-334">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="bdf34-335">Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-335">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bdf34-336">Stellen Sie sicher, dass `UseStatusCodePagesWithReExecute` vor `UseRouting` platziert wird, damit die Anforderung an die Statusseite umgeleitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="bdf34-336">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="bdf34-337">Ein Razor Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="bdf34-337">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="bdf34-338">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="bdf34-338">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="bdf34-339">Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-339">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="bdf34-340">Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="bdf34-340">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="bdf34-341">Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="bdf34-341">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="bdf34-342">Die Vorlagen für URL und Abfragezeichenfolgen können für den Statuscode einen Platzhalter (`{0}`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-342">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="bdf34-343">Die URL-Vorlage muss mit einem Schrägstrich (`/`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-343">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="bdf34-344">Wenn Sie im Pfad einen Platzhalter verwenden, vergewissern Sie sich, dass der Endpunkt (Seite oder Controller) das Pfadsegment verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="bdf34-344">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="bdf34-345">Eine Razor Page für Fehler sollte z. B. den Wert des optionalen Pfadsegments mit der `@page`-Anweisung akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="bdf34-345">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="bdf34-346">Der Endpunkt, der den Fehler verarbeitet, kann die ursprüngliche URL abrufen, die den Fehler generiert hat (siehe folgendes Beispiel):</span><span class="sxs-lookup"><span data-stu-id="bdf34-346">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="bdf34-347">Deaktivieren von Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="bdf34-347">Disable status code pages</span></span>

<span data-ttu-id="bdf34-348">Verwenden Sie das Attribut [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute), um Statuscodeseiten für einen MVC-Controller oder eine MVC-Aktionsmethode zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="bdf34-348">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="bdf34-349">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>, um Statuscodeseiten für bestimmte Anforderungen in der Handlermethode von Razor Pages oder in einem MVC-Controller zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="bdf34-349">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="bdf34-350">Ausnahmebehandlungscode</span><span class="sxs-lookup"><span data-stu-id="bdf34-350">Exception-handling code</span></span>

<span data-ttu-id="bdf34-351">Code auf Seiten zur Ausnahmebehandlung kann Ausnahmen auslösen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-351">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="bdf34-352">Es ist häufig empfehlenswert, wenn Produktionsfehlerseiten nur statische Inhalte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-352">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="bdf34-353">Antwortheader</span><span class="sxs-lookup"><span data-stu-id="bdf34-353">Response headers</span></span>

<span data-ttu-id="bdf34-354">Nachdem die Header für eine Antwort gesendet wurden:</span><span class="sxs-lookup"><span data-stu-id="bdf34-354">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="bdf34-355">Die App kann den Statuscode der Antwort nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="bdf34-355">The app can't change the response's status code.</span></span>
* <span data-ttu-id="bdf34-356">Weder Ausnahmeseiten noch Handler können ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-356">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="bdf34-357">Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-357">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="bdf34-358">Sichere Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="bdf34-358">Server exception handling</span></span>

<span data-ttu-id="bdf34-359">Neben der Ausnahmebehandlungslogik in Ihrer App kann die [HTTP-Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-359">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="bdf34-360">Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort *500 – Interner Serverfehler* ohne Antworttext.</span><span class="sxs-lookup"><span data-stu-id="bdf34-360">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="bdf34-361">Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="bdf34-361">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="bdf34-362">Anforderungen, die nicht von Ihrer App verarbeitet werden, werden vom Server verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="bdf34-362">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="bdf34-363">Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="bdf34-363">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="bdf34-364">Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-364">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="bdf34-365">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="bdf34-365">Startup exception handling</span></span>

<span data-ttu-id="bdf34-366">Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten.</span><span class="sxs-lookup"><span data-stu-id="bdf34-366">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="bdf34-367">Der Host kann für das [Erfassen von Startfehlern](xref:fundamentals/host/web-host#capture-startup-errors) und [Erfassen detaillierter Fehler](xref:fundamentals/host/web-host#detailed-errors) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-367">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="bdf34-368">Die Hostingebene kann nur dann für einen beim Start erfassten Fehler eine Fehlerseite anzeigen, wenn der Fehler nach der Bindung an die Hostadresse bzw. den Port auftritt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-368">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="bdf34-369">Wenn die Bindung misslingt:</span><span class="sxs-lookup"><span data-stu-id="bdf34-369">If binding fails:</span></span>

* <span data-ttu-id="bdf34-370">Die Hostebene protokolliert eine kritische Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="bdf34-370">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="bdf34-371">Der DotNet-Prozess stürzt ab.</span><span class="sxs-lookup"><span data-stu-id="bdf34-371">The dotnet process crashes.</span></span>
* <span data-ttu-id="bdf34-372">Wenn der HTTP-Server [Kestrel](xref:fundamentals/servers/kestrel) heißt, wird keine Fehlerseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-372">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="bdf34-373">Wenn sie auf [IIS](/iis) (oder Azure App Service) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann.</span><span class="sxs-lookup"><span data-stu-id="bdf34-373">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="bdf34-374">Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="bdf34-374">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="bdf34-375">Datenbank-Fehlerseite</span><span class="sxs-lookup"><span data-stu-id="bdf34-375">Database error page</span></span>

<span data-ttu-id="bdf34-376">Die Middleware „Datenbank-Fehlerseite“ erfasst datenbankbezogene Ausnahmen, die mithilfe von Entity Framework-Migrationen aufgelöst werden können.</span><span class="sxs-lookup"><span data-stu-id="bdf34-376">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="bdf34-377">Wenn diese Ausnahmen auftreten, wird eine HTML-Antwort mit Details zu möglichen Aktionen zum Beheben des Problems generiert.</span><span class="sxs-lookup"><span data-stu-id="bdf34-377">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="bdf34-378">Diese Seite darf nur in der Entwicklungsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-378">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="bdf34-379">Aktivieren Sie die Seite, indem Sie `Startup.Configure` Code hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="bdf34-379">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="bdf34-380"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> benötigt das [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="bdf34-380"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="bdf34-381">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="bdf34-381">Exception filters</span></span>

<span data-ttu-id="bdf34-382">In MVC-Apps können Ausnahmefilter global oder auf einen Controller oder eine Aktion bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-382">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="bdf34-383">In Razor Pages-Apps können sie global oder auf ein Seitenmodell bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-383">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="bdf34-384">Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt.</span><span class="sxs-lookup"><span data-stu-id="bdf34-384">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="bdf34-385">Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="bdf34-385">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="bdf34-386">Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen auftreten. Sie sind jedoch nicht so flexibel wie Middleware für die Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="bdf34-386">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="bdf34-387">Es wird empfohlen, die Middleware zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="bdf34-387">We recommend using the middleware.</span></span> <span data-ttu-id="bdf34-388">Verwenden Sie Filter nur dann, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine andere Strategie wählen müssen.</span><span class="sxs-lookup"><span data-stu-id="bdf34-388">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="bdf34-389">Modellstatusfehler</span><span class="sxs-lookup"><span data-stu-id="bdf34-389">Model state errors</span></span>

<span data-ttu-id="bdf34-390">Informationen zum Behandeln von Modellstatusfehlern finden Sie unter [Modellbindung](xref:mvc/models/model-binding) und [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="bdf34-390">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bdf34-391">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bdf34-391">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
