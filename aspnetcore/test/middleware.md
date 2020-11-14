---
title: Testen von ASP.NET Core-Middleware
author: tratcher
description: Erfahren Sie, wie ASP.NET Core-Middleware mit TestServer testen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
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
uid: test/middleware
ms.openlocfilehash: 2dd5fa127af4432c612bb654d50eb4147aea6868
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051433"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="13adc-103">Testen von ASP.NET Core-Middleware</span><span class="sxs-lookup"><span data-stu-id="13adc-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="13adc-104">Von [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="13adc-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="13adc-105">Middleware kann mit <xref:Microsoft.AspNetCore.TestHost.TestServer> isoliert getestet werden.</span><span class="sxs-lookup"><span data-stu-id="13adc-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="13adc-106">Die Funktion ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="13adc-106">It allows you to:</span></span>

* <span data-ttu-id="13adc-107">Instanziieren Sie eine App-Pipeline, die nur die Komponenten enthält, die Sie testen müssen.</span><span class="sxs-lookup"><span data-stu-id="13adc-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="13adc-108">Senden Sie benutzerdefinierte Anforderungen, um das Verhalten der Middleware zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="13adc-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="13adc-109">Vorteile:</span><span class="sxs-lookup"><span data-stu-id="13adc-109">Advantages:</span></span>

* <span data-ttu-id="13adc-110">Anforderungen werden arbeitsspeicherintern gesendet und nicht über das Netzwerk serialisiert.</span><span class="sxs-lookup"><span data-stu-id="13adc-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="13adc-111">Dadurch werden zusätzliche Aspekte wie Portverwaltung und HTTPS-Zertifikate vermieden.</span><span class="sxs-lookup"><span data-stu-id="13adc-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="13adc-112">Ausnahmen in der Middleware können direkt an den aufrufenden Test zurückfließen.</span><span class="sxs-lookup"><span data-stu-id="13adc-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="13adc-113">Es ist möglich, Serverdatenstrukturen, wie z. B. <xref:Microsoft.AspNetCore.Http.HttpContext>, direkt im Test anzupassen.</span><span class="sxs-lookup"><span data-stu-id="13adc-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="13adc-114">Einrichten von TestServer</span><span class="sxs-lookup"><span data-stu-id="13adc-114">Set up the TestServer</span></span>

<span data-ttu-id="13adc-115">Erstellen Sie im Testprojekt einen Test:</span><span class="sxs-lookup"><span data-stu-id="13adc-115">In the test project, create a test:</span></span>

* <span data-ttu-id="13adc-116">Erstellen und starten Sie einen Host, der <xref:Microsoft.AspNetCore.TestHost.TestServer> verwendet.</span><span class="sxs-lookup"><span data-stu-id="13adc-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="13adc-117">Fügen Sie alle benötigten Dienste hinzu, die die Middleware verwendet.</span><span class="sxs-lookup"><span data-stu-id="13adc-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="13adc-118">Fügen dem Projekt das NuGet-Paket [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) hinzu:</span><span class="sxs-lookup"><span data-stu-id="13adc-118">Add the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) NuGet package to the project:</span></span>
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* <span data-ttu-id="13adc-119">Konfigurieren Sie die Verarbeitungspipeline für die Nutzung der Middleware für den Test.</span><span class="sxs-lookup"><span data-stu-id="13adc-119">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="13adc-120">Senden von Anforderungen mit HttpClient</span><span class="sxs-lookup"><span data-stu-id="13adc-120">Send requests with HttpClient</span></span>

<span data-ttu-id="13adc-121">Senden Sie eine Anforderung mit <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="13adc-121">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="13adc-122">Bestätigen Sie das Ergebnis.</span><span class="sxs-lookup"><span data-stu-id="13adc-122">Assert the result.</span></span> <span data-ttu-id="13adc-123">Machen Sie zunächst eine Assertion, die das Gegenteil des von Ihnen erwarteten Ergebnisses darstellt.</span><span class="sxs-lookup"><span data-stu-id="13adc-123">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="13adc-124">Ein erster Lauf mit einer falsch-positiven Assertion bestätigt, dass der Test fehlschlägt, wenn die Middleware einwandfrei funktioniert.</span><span class="sxs-lookup"><span data-stu-id="13adc-124">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="13adc-125">Führen Sie den Test aus, und bestätigen Sie, dass der Test fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="13adc-125">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="13adc-126">Im folgenden Beispiel sollte die Middleware den Statuscode 404 ( *Nicht gefunden* ) zurückgeben, wenn der Stammendpunkt angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="13adc-126">In the following example, the middleware should return a 404 status code ( *Not Found* ) when the root endpoint is requested.</span></span> <span data-ttu-id="13adc-127">Führen Sie den ersten Testlauf mit `Assert.NotEqual( ... );` durch, der fehlschlagen sollte:</span><span class="sxs-lookup"><span data-stu-id="13adc-127">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="13adc-128">Ändern Sie die Assertion so, dass die Middleware unter normalen Betriebsbedingungen getestet wird.</span><span class="sxs-lookup"><span data-stu-id="13adc-128">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="13adc-129">Im letzten Test wird `Assert.Equal( ... );` verwendet.</span><span class="sxs-lookup"><span data-stu-id="13adc-129">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="13adc-130">Führen Sie den Test erneut aus, um zu bestätigen, dass er bestanden wird.</span><span class="sxs-lookup"><span data-stu-id="13adc-130">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="13adc-131">Senden von Anforderungen mit HttpContext</span><span class="sxs-lookup"><span data-stu-id="13adc-131">Send requests with HttpContext</span></span>

<span data-ttu-id="13adc-132">Eine Test-App kann eine Anforderung auch mit [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A) senden.</span><span class="sxs-lookup"><span data-stu-id="13adc-132">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="13adc-133">Im folgenden Beispiel erfolgen mehrere Prüfungen, wenn `https://example.com/A/Path/?and=query` von der Middleware verarbeitet wird:</span><span class="sxs-lookup"><span data-stu-id="13adc-133">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="13adc-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> erlaubt die Direktkonfiguration eines <xref:Microsoft.AspNetCore.Http.HttpContext>-Objekts, anstatt die <xref:System.Net.Http.HttpClient>-Abstraktionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="13adc-134"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="13adc-135">Verwenden Sie <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>, um Strukturen zu manipulieren, die nur auf dem Server verfügbar sind, wie z. B. [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) oder [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="13adc-135">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="13adc-136">Wie im früheren Beispiel, das auf eine Antwort des Typs *404 – nicht gefunden* getestet wurde, überprüfen Sie das Gegenteil für jede `Assert`-Anweisung im vorhergehenden Test.</span><span class="sxs-lookup"><span data-stu-id="13adc-136">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="13adc-137">Die Überprüfung bestätigt, dass der Test bei normalem Betrieb der Middleware ordnungsgemäß fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="13adc-137">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="13adc-138">Nachdem Sie bestätigt haben, dass der falsch positive Test funktioniert, legen Sie die endgültigen `Assert`-Anweisungen für die erwarteten Bedingungen und Werte des Tests fest.</span><span class="sxs-lookup"><span data-stu-id="13adc-138">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="13adc-139">Führen Sie ihn erneut aus, um zu bestätigen, dass er bestanden wird.</span><span class="sxs-lookup"><span data-stu-id="13adc-139">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="13adc-140">TestServer-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="13adc-140">TestServer limitations</span></span>

<span data-ttu-id="13adc-141">TestServer:</span><span class="sxs-lookup"><span data-stu-id="13adc-141">TestServer:</span></span>

* <span data-ttu-id="13adc-142">Wurde erstellt, um Serververhalten zum Testen von Middleware zu replizieren.</span><span class="sxs-lookup"><span data-stu-id="13adc-142">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="13adc-143">Hierbei wird **nicht** versucht, das gesamte <xref:System.Net.Http.HttpClient>-Verhalten zu replizieren.</span><span class="sxs-lookup"><span data-stu-id="13adc-143">Does \* **not** _ try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
<span data-ttu-id="13adc-144">Es wird versucht, dem Client so viel Kontrolle über den Server wie möglich zu geben, mit weitestgehendem Einblick in die Vorgänge auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="13adc-144">_ Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="13adc-145">Beispielsweise können Ausnahmen ausgelöst werden, die normalerweise nicht von `HttpClient` ausgelöst werden, um den Serverzustand direkt zu kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="13adc-145">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="13adc-146">Standardmäßig werden einige transportspezifische Header nicht festgelegt, da diese in der Regel für Middleware nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="13adc-146">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="13adc-147">Weitere Informationen finden Sie im nächsten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="13adc-147">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="13adc-148">Content-Length- und Transfer-Encoding-Header</span><span class="sxs-lookup"><span data-stu-id="13adc-148">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="13adc-149">TestServer legt **keine** transportbezogenen Anforderungs- oder Antwortheader wie [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) oder [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding) fest.</span><span class="sxs-lookup"><span data-stu-id="13adc-149">TestServer does \* **not** _ set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="13adc-150">In Anwendungen sollten Abhängigkeiten von diesen Headern vermieden werden, weil ihre Verwendung je nach Client, Szenario und Protokoll variiert.</span><span class="sxs-lookup"><span data-stu-id="13adc-150">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="13adc-151">Wenn `Content-Length` und `Transfer-Encoding` erforderlich sind, um ein bestimmtes Szenario zu testen, können sie im Test angegeben werden, wenn <xref:System.Net.Http.HttpRequestMessage> oder <xref:Microsoft.AspNetCore.Http.HttpContext> verfasst wird.</span><span class="sxs-lookup"><span data-stu-id="13adc-151">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="13adc-152">Weitere Informationen finden Sie in den folgenden GitHub-Issues:</span><span class="sxs-lookup"><span data-stu-id="13adc-152">For more information, see the following GitHub issues:</span></span>

<span data-ttu-id="13adc-153">[dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)</span><span class="sxs-lookup"><span data-stu-id="13adc-153">_ [dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)</span></span>
* [<span data-ttu-id="13adc-154">dotnet/aspnetcore#18463</span><span class="sxs-lookup"><span data-stu-id="13adc-154">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="13adc-155">dotnet/aspnetcore#13273</span><span class="sxs-lookup"><span data-stu-id="13adc-155">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
