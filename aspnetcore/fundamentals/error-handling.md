---
title: Fehlerbehandlung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: a1f40bdcdd4f2472aa86b311bfd9302e6aa8adc0
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635098"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="603e2-103">Fehlerbehandlung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="603e2-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="603e2-104">Von [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="603e2-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="603e2-105">Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="603e2-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="603e2-106">Weitere Informationen für Web-Apps finden Sie unter <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="603e2-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="603e2-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="603e2-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="603e2-108">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample).) Der Artikel enthält Anweisungen zum Festlegen von Präprozessordirektiven (`#if`, `#endif`, `#define`) in der Beispiel-App, um verschiedene Szenarien zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="603e2-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="603e2-109">Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="603e2-109">Developer Exception Page</span></span>

<span data-ttu-id="603e2-110">Die *Seite mit Ausnahmen für Entwickler* enthält ausführliche Informationen zu Anforderungsausnahmen.</span><span class="sxs-lookup"><span data-stu-id="603e2-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="603e2-111">Die Seite wird von der `Microsoft.AspNetCore.Diagnostics`-Assembly bereitgestellt, die sich im [`Microsoft.AspNetCore.App` gemeinsam verwendeten Framework](xref:fundamentals/metapackage-app) befindet.</span><span class="sxs-lookup"><span data-stu-id="603e2-111">The page is made available by the `Microsoft.AspNetCore.Diagnostics` assembly, which is in the [`Microsoft.AspNetCore.App` shared framework](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="603e2-112">Fügen Sie der `Startup.Configure`-Methode Code zum Aktivieren der Seite hinzu, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="603e2-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="603e2-113">Fügen Sie den Aufruf von <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> vor Middleware an der Stelle ein, an der Sie Ausnahmen abfangen möchten.</span><span class="sxs-lookup"><span data-stu-id="603e2-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="603e2-114">Aktivieren Sie die Seite mit Ausnahmen für Entwickler **nur dann, wenn die App in der Entwicklungsumgebung ausgeführt wird**.</span><span class="sxs-lookup"><span data-stu-id="603e2-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="603e2-115">Wenn die App in der Produktionsumgebung ausgeführt wird, sollten Sie keine detaillierten Ausnahmeinformationen öffentlich teilen.</span><span class="sxs-lookup"><span data-stu-id="603e2-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="603e2-116">Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="603e2-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="603e2-117">Die Seite enthält die folgenden Informationen zu der Ausnahme und der Anforderung:</span><span class="sxs-lookup"><span data-stu-id="603e2-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="603e2-118">Stapelüberwachung</span><span class="sxs-lookup"><span data-stu-id="603e2-118">Stack trace</span></span>
* <span data-ttu-id="603e2-119">Abfragezeichenfolgeparameter (sofern vorhanden)</span><span class="sxs-lookup"><span data-stu-id="603e2-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="603e2-120">Cookies (ggf.)</span><span class="sxs-lookup"><span data-stu-id="603e2-120">Cookies (if any)</span></span>
* <span data-ttu-id="603e2-121">Header</span><span class="sxs-lookup"><span data-stu-id="603e2-121">Headers</span></span>

<span data-ttu-id="603e2-122">Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) die Seite mit Ausnahmen für Entwickler anzuzeigen, verwenden Sie die Präprozessordirektive `DevEnvironment`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="603e2-123">Seite „Ausnahmehandler“</span><span class="sxs-lookup"><span data-stu-id="603e2-123">Exception handler page</span></span>

<span data-ttu-id="603e2-124">Um eine benutzerdefinierte Fehlerbehandlung für die Produktionsumgebung zu konfigurieren, verwenden Sie Middleware zur Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="603e2-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="603e2-125">Die Middleware:</span><span class="sxs-lookup"><span data-stu-id="603e2-125">The middleware:</span></span>

* <span data-ttu-id="603e2-126">Dient zum Abfangen und Protokollieren von Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="603e2-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="603e2-127">Führt die Anforderung für die angegebene Seite oder den Controller in einer anderen Pipeline erneut aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="603e2-128">Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="603e2-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="603e2-129">Im folgenden Beispiel fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu:</span><span class="sxs-lookup"><span data-stu-id="603e2-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="603e2-130">Die Razor Pages-App-Vorlage stellt im Ordner *Pages* eine Fehlerseite ( *.cshtml*) und <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel>-Klasse (`ErrorModel`) bereit.</span><span class="sxs-lookup"><span data-stu-id="603e2-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="603e2-131">Für eine MVC-App enthält die Projektvorlage die Aktionsmethode „Error“ und die Ansicht „Error“.</span><span class="sxs-lookup"><span data-stu-id="603e2-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="603e2-132">Es folgt die Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="603e2-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="603e2-133">Markieren Sie die Aktionsmethode für die Fehlerbehandlung nicht mit HTTP-Methodenattributen wie `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="603e2-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="603e2-134">Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt.</span><span class="sxs-lookup"><span data-stu-id="603e2-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="603e2-135">Lassen Sie den anonymen Zugriff auf die Methode zu, damit nicht authentifizierte Benutzer die Fehleransicht empfangen können.</span><span class="sxs-lookup"><span data-stu-id="603e2-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="603e2-136">Zugreifen auf die Ausnahme</span><span class="sxs-lookup"><span data-stu-id="603e2-136">Access the exception</span></span>

<span data-ttu-id="603e2-137">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Controller für die Fehlerbehandlung oder auf einer Seite zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="603e2-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="603e2-138">Stellen Sie Clients **keine** vertraulichen Fehlerinformationen bereit.</span><span class="sxs-lookup"><span data-stu-id="603e2-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="603e2-139">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="603e2-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="603e2-140">Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) die Seite für die Ausnahmebehandlung anzuzeigen, verwenden Sie die Präprozessordirektiven `ProdEnvironment` und `ErrorHandlerPage`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="603e2-141">Lambda-Ausdruck für Ausnahmehandler</span><span class="sxs-lookup"><span data-stu-id="603e2-141">Exception handler lambda</span></span>

<span data-ttu-id="603e2-142">Eine Alternative zu einer [benutzerdefinierten Ausnahmebehandlungsseite](#exception-handler-page) ist das Angeben eines Lambda-Ausdrucks für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="603e2-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="603e2-143">Die Verwendung eines Lambda-Ausdrucks ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="603e2-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="603e2-144">Hier ist ein Beispiel der Verwendung eines Lambda-Ausdrucks für die Ausnahmebehandlung:</span><span class="sxs-lookup"><span data-stu-id="603e2-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="603e2-145">Im vorangehenden Code wird `await context.Response.WriteAsync(new string(' ', 512));` hinzugefügt, sodass der Internet Explorer-Browser die Fehlermeldung anstelle einer IE-Fehlermeldung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="603e2-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="603e2-146">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="603e2-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="603e2-147">Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit.</span><span class="sxs-lookup"><span data-stu-id="603e2-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="603e2-148">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="603e2-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="603e2-149">Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) das Ergebnis des Lambda-Ausdrucks für die Ausnahmebehandlung anzuzeigen, verwenden Sie die Präprozessordirektiven `ProdEnvironment` und `ErrorHandlerLambda`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="603e2-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="603e2-150">UseStatusCodePages</span></span>

<span data-ttu-id="603e2-151">Ihre ASP.NET Core-App stellt für HTTP-Statuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="603e2-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="603e2-152">Die App gibt einen Statuscode und einen leeren Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="603e2-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="603e2-153">Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.</span><span class="sxs-lookup"><span data-stu-id="603e2-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="603e2-154">Die Middleware wird vom Paket [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) zur Verfügung gestellt, das im [Metapaket „Microsoft.AspNetCore.App“](xref:fundamentals/metapackage-app) enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="603e2-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="603e2-155">Um für gängige Statuscodes einfache Handler im Textformat zu aktivieren, rufen Sie in der `Startup.Configure`-Methode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> auf:</span><span class="sxs-lookup"><span data-stu-id="603e2-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="603e2-156">Rufen Sie `UseStatusCodePages` vor Middleware zur Anforderungsverarbeitung auf (z.B. Middleware für statische Dateien und MVC-Middleware).</span><span class="sxs-lookup"><span data-stu-id="603e2-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="603e2-157">Hier ist ein Beispiel des von den Standardhandlern angezeigten Texts:</span><span class="sxs-lookup"><span data-stu-id="603e2-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="603e2-158">Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) eines der verschiedenen Formate für Statuscodeseiten anzuzeigen, verwenden Sie eine der Präprozessordirektiven, die mit `StatusCodePages` beginnen, und wählen Sie auf der Startseite **404 auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="603e2-159">UseStatusCodePages mit Formatzeichenfolge</span><span class="sxs-lookup"><span data-stu-id="603e2-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="603e2-160">Um den Inhaltstyp und Text der Antwort anzupassen, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Inhaltstyp und eine Formatierungszeichenfolge erfordert:</span><span class="sxs-lookup"><span data-stu-id="603e2-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="603e2-161">UseStatusCodePages mit Lambda-Ausdruck</span><span class="sxs-lookup"><span data-stu-id="603e2-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="603e2-162">Um benutzerdefinierten Code für die Fehlerbehandlung und das Schreiben von Antworten anzugeben, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Lambda-Ausdruck verwendet:</span><span class="sxs-lookup"><span data-stu-id="603e2-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="603e2-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="603e2-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="603e2-164">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="603e2-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="603e2-165">Sendet den Statuscode *302 Found* (Gefunden) an den Client.</span><span class="sxs-lookup"><span data-stu-id="603e2-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="603e2-166">Der Client wird an den in der URL-Vorlage angegebenen Standort umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="603e2-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="603e2-167">Die URL-Vorlage kann, wie im Beispiel gezeigt, einen `{0}`-Platzhalter für den Statuscode enthalten.</span><span class="sxs-lookup"><span data-stu-id="603e2-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="603e2-168">Wenn die URL-Vorlage mit einer Tilde (~) beginnt, wird die Tilde durch die Angabe für `PathBase` der App ersetzt.</span><span class="sxs-lookup"><span data-stu-id="603e2-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="603e2-169">Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="603e2-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="603e2-170">Ein Razor Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="603e2-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="603e2-171">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="603e2-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="603e2-172">Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="603e2-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="603e2-173">Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="603e2-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="603e2-174">Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="603e2-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="603e2-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="603e2-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="603e2-176">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="603e2-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="603e2-177">Gibt den ursprünglichen Statuscode an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="603e2-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="603e2-178">Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.</span><span class="sxs-lookup"><span data-stu-id="603e2-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="603e2-179">Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder Razor-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="603e2-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="603e2-180">Stellen Sie sicher, dass `UseStatusCodePagesWithReExecute` vor `UseRouting` platziert wird, damit die Anforderung an die Statusseite umgeleitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="603e2-180">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="603e2-181">Ein Razor Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="603e2-181">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="603e2-182">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="603e2-182">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="603e2-183">Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="603e2-183">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="603e2-184">Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="603e2-184">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="603e2-185">Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="603e2-185">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="603e2-186">Die Vorlagen für URL und Abfragezeichenfolgen können für den Statuscode einen Platzhalter (`{0}`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="603e2-186">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="603e2-187">Die URL-Vorlage muss mit einem Schrägstrich (`/`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="603e2-187">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="603e2-188">Wenn Sie im Pfad einen Platzhalter verwenden, vergewissern Sie sich, dass der Endpunkt (Seite oder Controller) das Pfadsegment verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="603e2-188">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="603e2-189">Eine Razor Page für Fehler sollte z. B. den Wert des optionalen Pfadsegments mit der `@page`-Anweisung akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="603e2-189">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="603e2-190">Der Endpunkt, der den Fehler verarbeitet, kann die ursprüngliche URL abrufen, die den Fehler generiert hat (siehe folgendes Beispiel):</span><span class="sxs-lookup"><span data-stu-id="603e2-190">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="603e2-191">Deaktivieren von Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="603e2-191">Disable status code pages</span></span>

<span data-ttu-id="603e2-192">Verwenden Sie das Attribut [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute), um Statuscodeseiten für einen MVC-Controller oder eine MVC-Aktionsmethode zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="603e2-192">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="603e2-193">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>, um Statuscodeseiten für bestimmte Anforderungen in der Handlermethode von Razor Pages oder in einem MVC-Controller zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="603e2-193">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="603e2-194">Ausnahmebehandlungscode</span><span class="sxs-lookup"><span data-stu-id="603e2-194">Exception-handling code</span></span>

<span data-ttu-id="603e2-195">Code auf Seiten zur Ausnahmebehandlung kann Ausnahmen auslösen.</span><span class="sxs-lookup"><span data-stu-id="603e2-195">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="603e2-196">Es ist häufig empfehlenswert, wenn Produktionsfehlerseiten nur statische Inhalte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="603e2-196">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="603e2-197">Antwortheader</span><span class="sxs-lookup"><span data-stu-id="603e2-197">Response headers</span></span>

<span data-ttu-id="603e2-198">Nachdem die Header für eine Antwort gesendet wurden:</span><span class="sxs-lookup"><span data-stu-id="603e2-198">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="603e2-199">Die App kann den Statuscode der Antwort nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="603e2-199">The app can't change the response's status code.</span></span>
* <span data-ttu-id="603e2-200">Weder Ausnahmeseiten noch Handler können ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="603e2-200">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="603e2-201">Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="603e2-201">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="603e2-202">Sichere Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="603e2-202">Server exception handling</span></span>

<span data-ttu-id="603e2-203">Neben der Ausnahmebehandlungslogik in Ihrer App kann die [HTTP-Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="603e2-203">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="603e2-204">Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort *500 – Interner Serverfehler* ohne Antworttext.</span><span class="sxs-lookup"><span data-stu-id="603e2-204">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="603e2-205">Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="603e2-205">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="603e2-206">Anforderungen, die nicht von Ihrer App verarbeitet werden, werden vom Server verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="603e2-206">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="603e2-207">Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="603e2-207">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="603e2-208">Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="603e2-208">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="603e2-209">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="603e2-209">Startup exception handling</span></span>

<span data-ttu-id="603e2-210">Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten.</span><span class="sxs-lookup"><span data-stu-id="603e2-210">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="603e2-211">Der Host kann für das [Erfassen von Startfehlern](xref:fundamentals/host/web-host#capture-startup-errors) und [Erfassen detaillierter Fehler](xref:fundamentals/host/web-host#detailed-errors) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="603e2-211">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="603e2-212">Die Hostingebene kann nur dann für einen beim Start erfassten Fehler eine Fehlerseite anzeigen, wenn der Fehler nach der Bindung an die Hostadresse bzw. den Port auftritt.</span><span class="sxs-lookup"><span data-stu-id="603e2-212">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="603e2-213">Wenn die Bindung misslingt:</span><span class="sxs-lookup"><span data-stu-id="603e2-213">If binding fails:</span></span>

* <span data-ttu-id="603e2-214">Die Hostebene protokolliert eine kritische Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="603e2-214">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="603e2-215">Der DotNet-Prozess stürzt ab.</span><span class="sxs-lookup"><span data-stu-id="603e2-215">The dotnet process crashes.</span></span>
* <span data-ttu-id="603e2-216">Wenn der HTTP-Server [Kestrel](xref:fundamentals/servers/kestrel) heißt, wird keine Fehlerseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="603e2-216">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="603e2-217">Wenn sie auf [IIS](/iis) (oder Azure App Service) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann.</span><span class="sxs-lookup"><span data-stu-id="603e2-217">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="603e2-218">Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="603e2-218">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="603e2-219">Datenbank-Fehlerseite</span><span class="sxs-lookup"><span data-stu-id="603e2-219">Database error page</span></span>

<span data-ttu-id="603e2-220">Die Middleware „Datenbank-Fehlerseite“ erfasst datenbankbezogene Ausnahmen, die mithilfe von Entity Framework-Migrationen aufgelöst werden können.</span><span class="sxs-lookup"><span data-stu-id="603e2-220">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="603e2-221">Wenn diese Ausnahmen auftreten, wird eine HTML-Antwort mit Details zu möglichen Aktionen zum Beheben des Problems generiert.</span><span class="sxs-lookup"><span data-stu-id="603e2-221">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="603e2-222">Diese Seite darf nur in der Entwicklungsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="603e2-222">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="603e2-223">Aktivieren Sie die Seite, indem Sie `Startup.Configure` Code hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="603e2-223">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="603e2-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> benötigt das [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="603e2-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="603e2-225">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="603e2-225">Exception filters</span></span>

<span data-ttu-id="603e2-226">In MVC-Apps können Ausnahmefilter global oder auf einen Controller oder eine Aktion bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="603e2-226">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="603e2-227">In Razor Pages-Apps können sie global oder auf ein Seitenmodell bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="603e2-227">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="603e2-228">Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt.</span><span class="sxs-lookup"><span data-stu-id="603e2-228">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="603e2-229">Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="603e2-229">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="603e2-230">Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen auftreten. Sie sind jedoch nicht so flexibel wie Middleware für die Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="603e2-230">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="603e2-231">Es wird empfohlen, die Middleware zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="603e2-231">We recommend using the middleware.</span></span> <span data-ttu-id="603e2-232">Verwenden Sie Filter nur dann, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine andere Strategie wählen müssen.</span><span class="sxs-lookup"><span data-stu-id="603e2-232">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="603e2-233">Modellstatusfehler</span><span class="sxs-lookup"><span data-stu-id="603e2-233">Model state errors</span></span>

<span data-ttu-id="603e2-234">Informationen zum Behandeln von Modellstatusfehlern finden Sie unter [Modellbindung](xref:mvc/models/model-binding) und [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="603e2-234">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="603e2-235">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="603e2-235">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
