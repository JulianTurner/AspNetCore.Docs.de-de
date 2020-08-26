---
title: Migrieren von HTTP-Handlern und Modulen zu ASP.net Core Middleware
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
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
uid: migration/http-modules
ms.openlocfilehash: 213807634a2a6990e9025de7871295cf97a81faf
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865587"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="06a8f-102">Migrieren von HTTP-Handlern und Modulen zu ASP.net Core Middleware</span><span class="sxs-lookup"><span data-stu-id="06a8f-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="06a8f-103">In diesem Artikel wird gezeigt, wie vorhandene ASP.net [-HTTP-Module und-Handler von System. Webserver](/iis/configuration/system.webserver/) zu ASP.net Core [Middleware](xref:fundamentals/middleware/index)migriert werden.</span><span class="sxs-lookup"><span data-stu-id="06a8f-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="06a8f-104">Wieder besuchte Module und Handler</span><span class="sxs-lookup"><span data-stu-id="06a8f-104">Modules and handlers revisited</span></span>

<span data-ttu-id="06a8f-105">Bevor Sie mit der ASP.net Core Middleware fortfahren, können Sie zuerst die Funktionsweise von HTTP-Modulen und-Handlern fortsetzen:</span><span class="sxs-lookup"><span data-stu-id="06a8f-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Module-Handler](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="06a8f-107">**Handler sind:**</span><span class="sxs-lookup"><span data-stu-id="06a8f-107">**Handlers are:**</span></span>

* <span data-ttu-id="06a8f-108">Klassen, die [IHttpHandler](/dotnet/api/system.web.ihttphandler) implementieren</span><span class="sxs-lookup"><span data-stu-id="06a8f-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="06a8f-109">Wird zum Verarbeiten von Anforderungen mit einem angegebenen Dateinamen oder einer Erweiterung verwendet, z *. b.. Report*</span><span class="sxs-lookup"><span data-stu-id="06a8f-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="06a8f-110">[Konfiguriert](/iis/configuration/system.webserver/handlers/) in *Web.config*</span><span class="sxs-lookup"><span data-stu-id="06a8f-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="06a8f-111">**Module:**</span><span class="sxs-lookup"><span data-stu-id="06a8f-111">**Modules are:**</span></span>

* <span data-ttu-id="06a8f-112">Klassen, die [IHttpModule](/dotnet/api/system.web.ihttpmodule) implementieren</span><span class="sxs-lookup"><span data-stu-id="06a8f-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="06a8f-113">Wird für jede Anforderung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="06a8f-113">Invoked for every request</span></span>

* <span data-ttu-id="06a8f-114">Kurzschluss (weitere Verarbeitung einer Anforderung wird beendet)</span><span class="sxs-lookup"><span data-stu-id="06a8f-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="06a8f-115">Der HTTP-Antwort kann hinzugefügt werden, oder es kann ein eigener erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="06a8f-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="06a8f-116">[Konfiguriert](/iis/configuration/system.webserver/modules/) in *Web.config*</span><span class="sxs-lookup"><span data-stu-id="06a8f-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="06a8f-117">**Die Reihenfolge, in der Module eingehende Anforderungen verarbeiten, wird durch Folgendes bestimmt:**</span><span class="sxs-lookup"><span data-stu-id="06a8f-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="06a8f-118">Der <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> , bei dem es sich um eine Reihe von Ereignissen handelt, die von ASP.net ausgelöst werden: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest)usw. Jedes Modul kann einen Handler für ein oder mehrere Ereignisse erstellen.</span><span class="sxs-lookup"><span data-stu-id="06a8f-118">The <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)>, which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="06a8f-119">Für dasselbe Ereignis die Reihenfolge, in der Sie in *Web.config*konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="06a8f-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="06a8f-120">Zusätzlich zu Modulen können Sie Handler für die Lebenszyklus Ereignisse ihrer *Global.asax.cs* -Datei hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="06a8f-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="06a8f-121">Diese Handler werden nach den Handlern in den konfigurierten Modulen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="06a8f-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="06a8f-122">Von Handlern und Modulen bis Middleware</span><span class="sxs-lookup"><span data-stu-id="06a8f-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="06a8f-123">**Middleware ist einfacher als HTTP-Module und-Handler:**</span><span class="sxs-lookup"><span data-stu-id="06a8f-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="06a8f-124">Module, Handler, *Global.asax.cs*, *Web.config* (außer IIS-Konfiguration) und der Lebenszyklus der Anwendung sind nicht mehr vorhanden.</span><span class="sxs-lookup"><span data-stu-id="06a8f-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="06a8f-125">Die Rollen von Modulen und Handlern wurden von Middleware übernommen.</span><span class="sxs-lookup"><span data-stu-id="06a8f-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="06a8f-126">Middleware wird nicht in *Web.config* sondern mithilfe von Code konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="06a8f-126">Middleware are configured using code rather than in *Web.config*</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="06a8f-127">Mithilfe der [Pipeline Verzweigung](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) können Sie Anforderungen an bestimmte Middleware senden. Dies basiert nicht nur auf der URL, sondern auch auf Anforderungs Headern, Abfrage Zeichenfolgen usw.</span><span class="sxs-lookup"><span data-stu-id="06a8f-127">[Pipeline branching](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="06a8f-128">Mithilfe der [Pipeline Verzweigung](xref:fundamentals/middleware/index#use-run-and-map) können Sie Anforderungen an bestimmte Middleware senden. Dies basiert nicht nur auf der URL, sondern auch auf Anforderungs Headern, Abfrage Zeichenfolgen usw.</span><span class="sxs-lookup"><span data-stu-id="06a8f-128">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

::: moniker-end

<span data-ttu-id="06a8f-129">**Middleware ähnelt Modulen stark:**</span><span class="sxs-lookup"><span data-stu-id="06a8f-129">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="06a8f-130">Wird im Prinzip für jede Anforderung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="06a8f-130">Invoked in principle for every request</span></span>

* <span data-ttu-id="06a8f-131">Eine Anforderung kann kurz gebunden werden, da [die Anforderung nicht an die nächste Middleware übergeben](#http-modules-shortcircuiting-middleware) wird.</span><span class="sxs-lookup"><span data-stu-id="06a8f-131">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="06a8f-132">Ihre eigene HTTP-Antwort kann erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="06a8f-132">Able to create their own HTTP response</span></span>

<span data-ttu-id="06a8f-133">**Middleware und Module werden in einer anderen Reihenfolge verarbeitet:**</span><span class="sxs-lookup"><span data-stu-id="06a8f-133">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="06a8f-134">Die Reihenfolge der Middleware basiert auf der Reihenfolge, in der Sie in die Anforderungs Pipeline eingefügt werden, während die Reihenfolge der Module hauptsächlich auf Ereignissen basiert. <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)></span><span class="sxs-lookup"><span data-stu-id="06a8f-134">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on <https://docs.microsoft.com/previous-versions/ms227673(v=vs.140)> events</span></span>

* <span data-ttu-id="06a8f-135">Die Reihenfolge der Middleware für Antworten ist das Gegenteil bei Anforderungen, während die Reihenfolge der Module für Anforderungen und Antworten gleich ist.</span><span class="sxs-lookup"><span data-stu-id="06a8f-135">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="06a8f-136">Weitere Informationen finden Sie [unter Erstellen einer middlewarepipeline mit iapplicationbuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="06a8f-136">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Middleware](http-modules/_static/middleware.png)

<span data-ttu-id="06a8f-138">Beachten Sie, dass in der obigen Abbildung die Authentifizierungs Middleware die Anforderung kurzgeschlossen hat.</span><span class="sxs-lookup"><span data-stu-id="06a8f-138">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="06a8f-139">Migrieren von Modulcode zu Middleware</span><span class="sxs-lookup"><span data-stu-id="06a8f-139">Migrating module code to middleware</span></span>

<span data-ttu-id="06a8f-140">Ein vorhandenes HTTP-Modul sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="06a8f-140">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="06a8f-141">Wie auf der Seite [Middleware](xref:fundamentals/middleware/index) gezeigt, ist eine ASP.net Core Middleware eine-Klasse, die eine-Methode verfügbar macht, die eine `Invoke` `HttpContext` -Klasse und eine zurückgibt `Task` .</span><span class="sxs-lookup"><span data-stu-id="06a8f-141">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="06a8f-142">Die neue Middleware sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="06a8f-142">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="06a8f-143">Die vorangehende Middleware-Vorlage stammt aus dem Abschnitt zum [Schreiben von Middleware](xref:fundamentals/middleware/write).</span><span class="sxs-lookup"><span data-stu-id="06a8f-143">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="06a8f-144">Die Hilfsklasse *mymiddlewareextensions* vereinfacht das Konfigurieren ihrer Middleware in der `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="06a8f-144">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="06a8f-145">Die- `UseMyMiddleware` Methode fügt der Anforderungs Pipeline die Middleware-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="06a8f-145">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="06a8f-146">Die von der Middleware benötigten Dienste werden in den Konstruktor der Middleware eingefügt.</span><span class="sxs-lookup"><span data-stu-id="06a8f-146">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="06a8f-147">Das Modul kann eine Anforderung beenden, z. b. wenn der Benutzer nicht autorisiert ist:</span><span class="sxs-lookup"><span data-stu-id="06a8f-147">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="06a8f-148">Eine Middleware verarbeitet dies, indem nicht `Invoke` für die nächste Middleware in der Pipeline aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="06a8f-148">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="06a8f-149">Beachten Sie, dass dies die Anforderung nicht vollständig beendet, da vorherige Middlewares weiterhin aufgerufen werden, wenn die Antwort durch die Pipeline wieder zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="06a8f-149">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="06a8f-150">Wenn Sie die Funktionalität Ihres Moduls zu ihrer neuen Middleware migrieren, stellen Sie möglicherweise fest, dass der Code nicht kompiliert wird, da die `HttpContext` Klasse in ASP.net Core erheblich geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="06a8f-150">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="06a8f-151">[Später](#migrating-to-the-new-httpcontext)erfahren Sie, wie Sie zum neuen ASP.net Core HttpContext migrieren.</span><span class="sxs-lookup"><span data-stu-id="06a8f-151">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="06a8f-152">Migrieren des Moduls in die Anforderungs Pipeline</span><span class="sxs-lookup"><span data-stu-id="06a8f-152">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="06a8f-153">HTTP-Module werden in der Regel mit *Web.config*der Anforderungs Pipeline hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="06a8f-153">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="06a8f-154">Konvertieren Sie diese, indem [Sie die neue Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) der Anforderungs Pipeline in ihrer Klasse hinzufügen `Startup` :</span><span class="sxs-lookup"><span data-stu-id="06a8f-154">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="06a8f-155">Die genaue Position in der Pipeline, an der Sie die neue Middleware einfügen, hängt von dem Ereignis ab, das als Modul ( `BeginRequest` , `EndRequest` usw.) behandelt wurde, und der Reihenfolge in der Liste der Module in *Web.config*.</span><span class="sxs-lookup"><span data-stu-id="06a8f-155">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="06a8f-156">Wie bereits erwähnt, gibt es keinen Anwendungslebenszyklus in ASP.net Core und die Reihenfolge, in der die Antworten von der Middleware verarbeitet werden, unterscheidet sich von der von Modulen verwendeten Reihenfolge.</span><span class="sxs-lookup"><span data-stu-id="06a8f-156">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="06a8f-157">Dies könnte dazu führen, dass Ihre Bestell Entscheidung schwieriger wird.</span><span class="sxs-lookup"><span data-stu-id="06a8f-157">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="06a8f-158">Wenn die Reihenfolge zu einem Problem wird, können Sie das Modul in mehrere middlewarekomponenten aufteilen, die unabhängig voneinander angeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="06a8f-158">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="06a8f-159">Migrieren von Handlercode zu Middleware</span><span class="sxs-lookup"><span data-stu-id="06a8f-159">Migrating handler code to middleware</span></span>

<span data-ttu-id="06a8f-160">Ein HTTP-Handler sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="06a8f-160">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="06a8f-161">In Ihrem ASP.net Core Projekt würden Sie dies in eine Middleware übersetzen, die in etwa wie folgt aussieht:</span><span class="sxs-lookup"><span data-stu-id="06a8f-161">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="06a8f-162">Diese Middleware ähnelt der Middleware, die den Modulen entspricht.</span><span class="sxs-lookup"><span data-stu-id="06a8f-162">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="06a8f-163">Der einzige wirkliche Unterschied besteht darin, dass hier kein-Rückruf erfolgt `_next.Invoke(context)` .</span><span class="sxs-lookup"><span data-stu-id="06a8f-163">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="06a8f-164">Das ist sinnvoll, da sich der Handler am Ende der Anforderungs Pipeline befindet, sodass keine nächste Middleware aufgerufen werden kann.</span><span class="sxs-lookup"><span data-stu-id="06a8f-164">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="06a8f-165">Migrieren von handlereinfügung in die Anforderungs Pipeline</span><span class="sxs-lookup"><span data-stu-id="06a8f-165">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="06a8f-166">Das Konfigurieren eines HTTP-Handlers erfolgt in *Web.config* und sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="06a8f-166">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="06a8f-167">Sie können dies konvertieren, indem Sie die neue handlermiddleware der Anforderungs Pipeline in ihrer Klasse hinzufügen `Startup` , ähnlich der von Modulen konvertierten Middleware.</span><span class="sxs-lookup"><span data-stu-id="06a8f-167">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="06a8f-168">Das Problem bei diesem Ansatz besteht darin, dass alle Anforderungen an die neue handlermiddleware gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="06a8f-168">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="06a8f-169">Es ist jedoch nur gewünscht, dass Anforderungen mit einer bestimmten Erweiterung ihre Middleware erreichen.</span><span class="sxs-lookup"><span data-stu-id="06a8f-169">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="06a8f-170">Dies bietet Ihnen die gleiche Funktionalität wie bei Ihrem HTTP-Handler.</span><span class="sxs-lookup"><span data-stu-id="06a8f-170">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="06a8f-171">Eine Lösung besteht darin, die Pipeline für Anforderungen mit einer bestimmten Erweiterung mithilfe der Erweiterungsmethode zu verzweigen `MapWhen` .</span><span class="sxs-lookup"><span data-stu-id="06a8f-171">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="06a8f-172">Dies geschieht in derselben Methode, in der `Configure` Sie die andere Middleware hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="06a8f-172">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="06a8f-173">`MapWhen` Diese Parameter werden von benötigt:</span><span class="sxs-lookup"><span data-stu-id="06a8f-173">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="06a8f-174">Ein Lambda, das den annimmt `HttpContext` und zurückgibt, `true` Wenn die Anforderung den Branch nach unten durchlaufen soll.</span><span class="sxs-lookup"><span data-stu-id="06a8f-174">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="06a8f-175">Dies bedeutet, dass Sie Anforderungen verzweigen können, die nicht nur auf Grundlage ihrer Erweiterung basieren, sondern auch auf Anforderungs Headern, Abfrage Zeichen folgen Parametern usw.</span><span class="sxs-lookup"><span data-stu-id="06a8f-175">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="06a8f-176">Ein Lambda, das eine annimmt `IApplicationBuilder` und alle Middleware für den Branch hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="06a8f-176">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="06a8f-177">Dies bedeutet, dass Sie der Verzweigung vor ihrer handlermiddleware zusätzliche Middleware hinzufügen können.</span><span class="sxs-lookup"><span data-stu-id="06a8f-177">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="06a8f-178">Der Pipeline hinzugefügte Middleware, bevor die Verzweigung für alle Anforderungen aufgerufen wird. die Verzweigung wirkt sich nicht auf diese aus.</span><span class="sxs-lookup"><span data-stu-id="06a8f-178">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="06a8f-179">Laden von Middleware-Optionen mit dem Options Muster</span><span class="sxs-lookup"><span data-stu-id="06a8f-179">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="06a8f-180">Einige Module und Handler verfügen über Konfigurationsoptionen, die in *Web.config*gespeichert werden. In ASP.net Core jedoch anstelle *Web.config*ein neues Konfigurations Modell verwendet.</span><span class="sxs-lookup"><span data-stu-id="06a8f-180">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="06a8f-181">Das neue [Konfigurationssystem](xref:fundamentals/configuration/index) bietet Ihnen folgende Optionen, um dieses Problem zu beheben:</span><span class="sxs-lookup"><span data-stu-id="06a8f-181">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="06a8f-182">Fügen Sie die Optionen direkt in die Middleware ein, wie im [nächsten Abschnitt](#loading-middleware-options-through-direct-injection)gezeigt.</span><span class="sxs-lookup"><span data-stu-id="06a8f-182">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="06a8f-183">Verwenden Sie das [options Muster](xref:fundamentals/configuration/options):</span><span class="sxs-lookup"><span data-stu-id="06a8f-183">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="06a8f-184">Erstellen Sie eine Klasse zum Speichern der Middleware-Optionen, z. b.:</span><span class="sxs-lookup"><span data-stu-id="06a8f-184">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="06a8f-185">Speichern der Optionswerte</span><span class="sxs-lookup"><span data-stu-id="06a8f-185">Store the option values</span></span>

   <span data-ttu-id="06a8f-186">Mit dem Konfigurationssystem können Sie Optionswerte beliebig speichern.</span><span class="sxs-lookup"><span data-stu-id="06a8f-186">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="06a8f-187">Bei den meisten Websites wird jedoch *appsettings.json*verwendet, daher wird dieser Ansatz verwendet:</span><span class="sxs-lookup"><span data-stu-id="06a8f-187">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="06a8f-188">*Mymiddlewareoptionssection* hier ist ein Abschnitts Name.</span><span class="sxs-lookup"><span data-stu-id="06a8f-188">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="06a8f-189">Er muss nicht mit dem Namen der Options Klasse identisch sein.</span><span class="sxs-lookup"><span data-stu-id="06a8f-189">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="06a8f-190">Zuordnen der Optionswerte zur Options-Klasse</span><span class="sxs-lookup"><span data-stu-id="06a8f-190">Associate the option values with the options class</span></span>

    <span data-ttu-id="06a8f-191">Das Options Muster verwendet das Framework für die Abhängigkeitsinjektion von ASP.net Core, um den Options Typ (z. b. `MyMiddlewareOptions` ) einem- `MyMiddlewareOptions` Objekt zuzuordnen, das über die tatsächlichen Optionen verfügt.</span><span class="sxs-lookup"><span data-stu-id="06a8f-191">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="06a8f-192">Aktualisieren Sie Ihre `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="06a8f-192">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="06a8f-193">Wenn Sie *appsettings.json*verwenden, fügen Sie es dem Konfigurations-Generator im- `Startup` Konstruktor hinzu:</span><span class="sxs-lookup"><span data-stu-id="06a8f-193">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="06a8f-194">Konfigurieren Sie den Options Dienst:</span><span class="sxs-lookup"><span data-stu-id="06a8f-194">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="06a8f-195">Ordnen Sie die Optionen der Options-Klasse zu:</span><span class="sxs-lookup"><span data-stu-id="06a8f-195">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="06a8f-196">Fügen Sie die Optionen in ihren Middleware-Konstruktor ein.</span><span class="sxs-lookup"><span data-stu-id="06a8f-196">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="06a8f-197">Dies ähnelt dem Einfügen von Optionen in einen Controller.</span><span class="sxs-lookup"><span data-stu-id="06a8f-197">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="06a8f-198">Die [usemiddleware](#http-modules-usemiddleware) -Erweiterungsmethode, mit der die Middleware hinzugefügt wird, `IApplicationBuilder` übernimmt die Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="06a8f-198">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="06a8f-199">Dies ist nicht auf- `IOptions` Objekte beschränkt.</span><span class="sxs-lookup"><span data-stu-id="06a8f-199">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="06a8f-200">Alle anderen Objekte, die von der Middleware benötigt werden, können auf diese Weise eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="06a8f-200">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="06a8f-201">Laden von Middleware-Optionen durch direkte Injektion</span><span class="sxs-lookup"><span data-stu-id="06a8f-201">Loading middleware options through direct injection</span></span>

<span data-ttu-id="06a8f-202">Das Options Muster hat den Vorteil, dass es eine lose Kopplung zwischen Options Werten und ihren Consumern erzeugt.</span><span class="sxs-lookup"><span data-stu-id="06a8f-202">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="06a8f-203">Nachdem Sie eine Options Klasse mit den tatsächlichen Options Werten verknüpft haben, kann jede andere Klasse über das Framework für die Abhängigkeitsinjektion Zugriff auf die Optionen erhalten.</span><span class="sxs-lookup"><span data-stu-id="06a8f-203">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="06a8f-204">Es ist nicht erforderlich, Optionswerte zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="06a8f-204">There's no need to pass around options values.</span></span>

<span data-ttu-id="06a8f-205">Dies wird jedoch unterbrochen, wenn Sie dieselbe Middleware zweimal mit unterschiedlichen Optionen verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="06a8f-205">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="06a8f-206">Beispielsweise eine Autorisierungs Middleware, die in verschiedenen Verzweigungen verwendet wird und verschiedene Rollen zulässt.</span><span class="sxs-lookup"><span data-stu-id="06a8f-206">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="06a8f-207">Sie können der One Options-Klasse nicht zwei verschiedene Options Objekte zuordnen.</span><span class="sxs-lookup"><span data-stu-id="06a8f-207">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="06a8f-208">Die Lösung besteht darin, die Options Objekte mit den tatsächlichen Options Werten in Ihrer `Startup` Klasse zu erhalten und diese direkt an jede Instanz Ihrer Middleware zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="06a8f-208">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="06a8f-209">Fügen Sie einen zweiten Schlüssel zum *appsettings.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="06a8f-209">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="06a8f-210">Verwenden Sie zum Hinzufügen eines zweiten Satzes von Optionen zum *appsettings.js* Datei einen neuen Schlüssel, um ihn eindeutig zu identifizieren:</span><span class="sxs-lookup"><span data-stu-id="06a8f-210">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="06a8f-211">Rufen Sie Optionswerte ab, und übergeben Sie Sie an die Middleware.</span><span class="sxs-lookup"><span data-stu-id="06a8f-211">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="06a8f-212">Die `Use...` Erweiterungsmethode (die die Middleware zur Pipeline hinzufügt) ist ein logischer Speicherort, um die Optionswerte zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="06a8f-212">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="06a8f-213">Aktivieren Sie die Middleware, um einen Optionsparameter zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="06a8f-213">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="06a8f-214">Stellen Sie eine Überladung der `Use...` Erweiterungsmethode bereit (die den options-Parameter annimmt und Sie an übergibt `UseMiddleware` ).</span><span class="sxs-lookup"><span data-stu-id="06a8f-214">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="06a8f-215">Wenn `UseMiddleware` mit Parametern aufgerufen wird, übergibt es die Parameter an Ihren Middleware-Konstruktor, wenn das Middleware-Objekt instanziiert wird.</span><span class="sxs-lookup"><span data-stu-id="06a8f-215">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="06a8f-216">Beachten Sie, dass dies das Options-Objekt in einem-Objekt umschließt `OptionsWrapper` .</span><span class="sxs-lookup"><span data-stu-id="06a8f-216">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="06a8f-217">Dies implementiert `IOptions` , wie vom Middleware-Konstruktor erwartet.</span><span class="sxs-lookup"><span data-stu-id="06a8f-217">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="06a8f-218">Migrieren zum neuen HttpContext</span><span class="sxs-lookup"><span data-stu-id="06a8f-218">Migrating to the new HttpContext</span></span>

<span data-ttu-id="06a8f-219">Sie haben bereits gesehen, dass die- `Invoke` Methode in der Middleware einen Parameter des Typs annimmt `HttpContext` :</span><span class="sxs-lookup"><span data-stu-id="06a8f-219">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="06a8f-220">`HttpContext` wurde in ASP.net Core erheblich geändert.</span><span class="sxs-lookup"><span data-stu-id="06a8f-220">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="06a8f-221">In diesem Abschnitt wird gezeigt, wie die am häufigsten verwendeten Eigenschaften von [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) in den neuen übersetzt werden `Microsoft.AspNetCore.Http.HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="06a8f-221">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="06a8f-222">HttpContext</span><span class="sxs-lookup"><span data-stu-id="06a8f-222">HttpContext</span></span>

<span data-ttu-id="06a8f-223">**HttpContext. Items** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-223">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="06a8f-224">**Eindeutige Anforderungs-ID (kein System. Web. HttpContext-Pendant)**</span><span class="sxs-lookup"><span data-stu-id="06a8f-224">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="06a8f-225">Gibt Ihnen eine eindeutige ID für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="06a8f-225">Gives you a unique id for each request.</span></span> <span data-ttu-id="06a8f-226">Sehr nützlich, um in Ihre Protokolle einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="06a8f-226">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="06a8f-227">HttpContext. Request</span><span class="sxs-lookup"><span data-stu-id="06a8f-227">HttpContext.Request</span></span>

<span data-ttu-id="06a8f-228">**HttpContext. Request. HttpMethod** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-228">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="06a8f-229">**HttpContext. Request. QueryString** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-229">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="06a8f-230">" **HttpContext. Request. URL** " und " **HttpContext. Request. RawUrl** " übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-230">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="06a8f-231">**HttpContext. Request. IsSecureConnection** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-231">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="06a8f-232">**HttpContext. Request. UserHostAddress** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-232">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="06a8f-233">**HttpContext. Request. Cookie s** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-233">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="06a8f-234">**HttpContext. Request. RequestContext. RouteData** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-234">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="06a8f-235">**HttpContext. Request. Headers** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-235">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="06a8f-236">**HttpContext. Request. UserAgent** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-236">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="06a8f-237">**HttpContext. Request. UrlReferrer** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-237">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="06a8f-238">**HttpContext. Request. ContentType** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-238">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="06a8f-239">**HttpContext. Request. Form** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-239">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="06a8f-240">Liest Formular Werte nur, wenn der Sub-Inhaltstyp " *x-www-form-urlencoded* " oder " *Form-Data*" ist.</span><span class="sxs-lookup"><span data-stu-id="06a8f-240">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="06a8f-241">**HttpContext. Request. InputStream** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-241">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="06a8f-242">Verwenden Sie diesen Code nur in einer Handlertyp-Middleware am Ende einer Pipeline.</span><span class="sxs-lookup"><span data-stu-id="06a8f-242">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="06a8f-243">Sie können den unformatierten Text nur einmal pro Anforderung lesen.</span><span class="sxs-lookup"><span data-stu-id="06a8f-243">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="06a8f-244">Middleware, die versucht, den Text nach dem ersten Lesevorgang zu lesen, liest einen leeren Text.</span><span class="sxs-lookup"><span data-stu-id="06a8f-244">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="06a8f-245">Dies gilt nicht für das Lesen eines Formulars wie zuvor gezeigt, da dies aus einem Puffer erfolgt.</span><span class="sxs-lookup"><span data-stu-id="06a8f-245">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="06a8f-246">HttpContext. Response</span><span class="sxs-lookup"><span data-stu-id="06a8f-246">HttpContext.Response</span></span>

<span data-ttu-id="06a8f-247">" **HttpContext. Response. Status** " und " **HttpContext. Response. StatusDescription** " übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-247">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="06a8f-248">" **HttpContext. Response. ContentEncoding** " und " **HttpContext. Response. ContentType** " übersetzen in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-248">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="06a8f-249">" **HttpContext. Response. ContentType** " selbst übersetzt ebenfalls Folgendes:</span><span class="sxs-lookup"><span data-stu-id="06a8f-249">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="06a8f-250">**HttpContext. Response. Output** übersetzt in:</span><span class="sxs-lookup"><span data-stu-id="06a8f-250">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="06a8f-251">**HttpContext. Response. TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="06a8f-251">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="06a8f-252">Die Einrichtung einer Datei wird [hier](../fundamentals/request-features.md#middleware-and-request-features)erläutert.</span><span class="sxs-lookup"><span data-stu-id="06a8f-252">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="06a8f-253">**HttpContext. Response. Headers**</span><span class="sxs-lookup"><span data-stu-id="06a8f-253">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="06a8f-254">Das Senden von Antwort Headern ist kompliziert, wenn Sie Sie nach dem Schreiben in den Antworttext festlegen, werden Sie nicht gesendet.</span><span class="sxs-lookup"><span data-stu-id="06a8f-254">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="06a8f-255">Die Lösung besteht darin, eine Rückruf Methode festzulegen, die vor dem Schreiben in die Antwort direkt aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="06a8f-255">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="06a8f-256">Dies wird am Anfang der `Invoke` Methode in ihrer Middleware am besten erreicht.</span><span class="sxs-lookup"><span data-stu-id="06a8f-256">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="06a8f-257">Diese Rückruf Methode legt die Antwortheader fest.</span><span class="sxs-lookup"><span data-stu-id="06a8f-257">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="06a8f-258">Der folgende Code legt eine Rückruf Methode namensfest `SetHeaders` :</span><span class="sxs-lookup"><span data-stu-id="06a8f-258">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="06a8f-259">Die `SetHeaders` Rückruf Methode würde wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="06a8f-259">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="06a8f-260">**HttpContext. Response. Cookie Hymnen**</span><span class="sxs-lookup"><span data-stu-id="06a8f-260">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="06a8f-261">Cookies wechseln zum Browser in einem *Set-Response Cookie -* Header.</span><span class="sxs-lookup"><span data-stu-id="06a8f-261">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="06a8f-262">Daher cookie erfordert das Senden von s denselben Rückruf wie zum Senden von Antwort Headern:</span><span class="sxs-lookup"><span data-stu-id="06a8f-262">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="06a8f-263">Die `SetCookies` Rückruf Methode würde wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="06a8f-263">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="06a8f-264">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="06a8f-264">Additional resources</span></span>

* [<span data-ttu-id="06a8f-265">Übersicht über HTTP-Handler und HTTP-Module</span><span class="sxs-lookup"><span data-stu-id="06a8f-265">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="06a8f-266">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="06a8f-266">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="06a8f-267">Anwendungsstart</span><span class="sxs-lookup"><span data-stu-id="06a8f-267">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="06a8f-268">Middleware</span><span class="sxs-lookup"><span data-stu-id="06a8f-268">Middleware</span></span>](xref:fundamentals/middleware/index)
