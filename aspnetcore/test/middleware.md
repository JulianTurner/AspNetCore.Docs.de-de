---
title: Testen von ASP.NET Core-Middleware
author: tratcher
description: Erfahren Sie, wie ASP.NET Core-Middleware mit TestServer testen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
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
uid: test/middleware
ms.openlocfilehash: 2dd5fa127af4432c612bb654d50eb4147aea6868
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051433"
---
# <a name="test-aspnet-core-middleware"></a>Testen von ASP.NET Core-Middleware

Von [Chris Ross](https://github.com/Tratcher)

Middleware kann mit <xref:Microsoft.AspNetCore.TestHost.TestServer> isoliert getestet werden. Die Funktion ermöglicht Folgendes:

* Instanziieren Sie eine App-Pipeline, die nur die Komponenten enthält, die Sie testen müssen.
* Senden Sie benutzerdefinierte Anforderungen, um das Verhalten der Middleware zu überprüfen.

Vorteile:

* Anforderungen werden arbeitsspeicherintern gesendet und nicht über das Netzwerk serialisiert.
* Dadurch werden zusätzliche Aspekte wie Portverwaltung und HTTPS-Zertifikate vermieden.
* Ausnahmen in der Middleware können direkt an den aufrufenden Test zurückfließen.
* Es ist möglich, Serverdatenstrukturen, wie z. B. <xref:Microsoft.AspNetCore.Http.HttpContext>, direkt im Test anzupassen.

## <a name="set-up-the-testserver"></a>Einrichten von TestServer

Erstellen Sie im Testprojekt einen Test:

* Erstellen und starten Sie einen Host, der <xref:Microsoft.AspNetCore.TestHost.TestServer> verwendet.
* Fügen Sie alle benötigten Dienste hinzu, die die Middleware verwendet.
* Fügen dem Projekt das NuGet-Paket [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost/) hinzu:
  
  ```dotnetcli
  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.TestHost" Version="3.1.*" />
  </ItemGroup>
  ```

* Konfigurieren Sie die Verarbeitungspipeline für die Nutzung der Middleware für den Test.

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a>Senden von Anforderungen mit HttpClient

Senden Sie eine Anforderung mit <xref:System.Net.Http.HttpClient>:

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

Bestätigen Sie das Ergebnis. Machen Sie zunächst eine Assertion, die das Gegenteil des von Ihnen erwarteten Ergebnisses darstellt. Ein erster Lauf mit einer falsch-positiven Assertion bestätigt, dass der Test fehlschlägt, wenn die Middleware einwandfrei funktioniert. Führen Sie den Test aus, und bestätigen Sie, dass der Test fehlschlägt.

Im folgenden Beispiel sollte die Middleware den Statuscode 404 ( *Nicht gefunden* ) zurückgeben, wenn der Stammendpunkt angefordert wird. Führen Sie den ersten Testlauf mit `Assert.NotEqual( ... );` durch, der fehlschlagen sollte:

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

Ändern Sie die Assertion so, dass die Middleware unter normalen Betriebsbedingungen getestet wird. Im letzten Test wird `Assert.Equal( ... );` verwendet. Führen Sie den Test erneut aus, um zu bestätigen, dass er bestanden wird.

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a>Senden von Anforderungen mit HttpContext

Eine Test-App kann eine Anforderung auch mit [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A) senden. Im folgenden Beispiel erfolgen mehrere Prüfungen, wenn `https://example.com/A/Path/?and=query` von der Middleware verarbeitet wird:

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

<xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> erlaubt die Direktkonfiguration eines <xref:Microsoft.AspNetCore.Http.HttpContext>-Objekts, anstatt die <xref:System.Net.Http.HttpClient>-Abstraktionen zu verwenden. Verwenden Sie <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>, um Strukturen zu manipulieren, die nur auf dem Server verfügbar sind, wie z. B. [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) oder [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).

Wie im früheren Beispiel, das auf eine Antwort des Typs *404 – nicht gefunden* getestet wurde, überprüfen Sie das Gegenteil für jede `Assert`-Anweisung im vorhergehenden Test. Die Überprüfung bestätigt, dass der Test bei normalem Betrieb der Middleware ordnungsgemäß fehlschlägt. Nachdem Sie bestätigt haben, dass der falsch positive Test funktioniert, legen Sie die endgültigen `Assert`-Anweisungen für die erwarteten Bedingungen und Werte des Tests fest. Führen Sie ihn erneut aus, um zu bestätigen, dass er bestanden wird.

## <a name="testserver-limitations"></a>TestServer-Einschränkungen

TestServer:

* Wurde erstellt, um Serververhalten zum Testen von Middleware zu replizieren.
* Hierbei wird **nicht** versucht, das gesamte <xref:System.Net.Http.HttpClient>-Verhalten zu replizieren.
Es wird versucht, dem Client so viel Kontrolle über den Server wie möglich zu geben, mit weitestgehendem Einblick in die Vorgänge auf dem Server. Beispielsweise können Ausnahmen ausgelöst werden, die normalerweise nicht von `HttpClient` ausgelöst werden, um den Serverzustand direkt zu kommunizieren.
* Standardmäßig werden einige transportspezifische Header nicht festgelegt, da diese in der Regel für Middleware nicht relevant sind. Weitere Informationen finden Sie im nächsten Abschnitt.

### <a name="content-length-and-transfer-encoding-headers"></a>Content-Length- und Transfer-Encoding-Header

TestServer legt **keine** transportbezogenen Anforderungs- oder Antwortheader wie [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) oder [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding) fest. In Anwendungen sollten Abhängigkeiten von diesen Headern vermieden werden, weil ihre Verwendung je nach Client, Szenario und Protokoll variiert. Wenn `Content-Length` und `Transfer-Encoding` erforderlich sind, um ein bestimmtes Szenario zu testen, können sie im Test angegeben werden, wenn <xref:System.Net.Http.HttpRequestMessage> oder <xref:Microsoft.AspNetCore.Http.HttpContext> verfasst wird. Weitere Informationen finden Sie in den folgenden GitHub-Issues:

[dotnet/aspnetcore#21677](https://github.com/dotnet/aspnetcore/issues/21677)
* [dotnet/aspnetcore#18463](https://github.com/dotnet/aspnetcore/issues/18463)
* [dotnet/aspnetcore#13273](https://github.com/dotnet/aspnetcore/issues/13273)
