---
title: Open Web Interface for .NET (OWIN) mit ASP.NET Core
author: ardalis
description: Erfahren Sie, wie ASP.NET Core die Open Web Interface for .NET (OWIN) unterstützt, die das Entkoppeln von Web-Apps von Webservern ermöglicht.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 12/18/2018
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
uid: fundamentals/owin
ms.openlocfilehash: 6085abc45137223d7a676107cf06dc2cf97a5c19
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060676"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="b82cd-103">Open Web Interface for .NET (OWIN) mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b82cd-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="b82cd-104">Von [Steve Smith](https://ardalis.com/) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b82cd-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b82cd-105">ASP.NET Core unterstützt Open Web Interface for .NET (OWIN).</span><span class="sxs-lookup"><span data-stu-id="b82cd-105">ASP.NET Core supports the Open Web Interface for .NET (OWIN).</span></span> <span data-ttu-id="b82cd-106">Mit OWIN können Web-Apps von Webservern entkoppelt werden.</span><span class="sxs-lookup"><span data-stu-id="b82cd-106">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="b82cd-107">OWIN legt eine Standardmöglichkeit des Einsatzes von Middleware in einer Pipeline zum Verarbeiten von Anforderungen und den entsprechenden Antworten fest.</span><span class="sxs-lookup"><span data-stu-id="b82cd-107">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="b82cd-108">ASP.NET Core-Anwendungen und -Middleware können mit auf OWIN basierten Anwendungen, Servern und OWIN basierter Middleware zusammenarbeiten.</span><span class="sxs-lookup"><span data-stu-id="b82cd-108">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="b82cd-109">OWIN bietet eine Entkopplungsebene, die das gemeinsame Verwenden zweier Frameworks mit unterschiedlichen Objektmodellen zulässt.</span><span class="sxs-lookup"><span data-stu-id="b82cd-109">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="b82cd-110">Das `Microsoft.AspNetCore.Owin`-Paket bietet zwei Adapterimplementierungen:</span><span class="sxs-lookup"><span data-stu-id="b82cd-110">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="b82cd-111">ASP.NET Core auf OWIN</span><span class="sxs-lookup"><span data-stu-id="b82cd-111">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="b82cd-112">OWIN auf ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b82cd-112">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="b82cd-113">So kann ASP.NET Core auf einem mit OWIN kompatiblen Server bzw. Host gehostet werden. Alternativ können andere mit OWIN kompatible Komponenten über ASP.NET Core ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b82cd-113">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b82cd-114">Das Verwenden dieser Adapter führt zu Leistungseinbußen.</span><span class="sxs-lookup"><span data-stu-id="b82cd-114">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="b82cd-115">Apps, die nur ASP.NET Core-Komponenten verwenden, sollten keine `Microsoft.AspNetCore.Owin`-Pakete oder Adapter verwenden.</span><span class="sxs-lookup"><span data-stu-id="b82cd-115">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="b82cd-116">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b82cd-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="b82cd-117">Ausführen von OWIN-Middleware in der ASP.NET Core-Pipeline</span><span class="sxs-lookup"><span data-stu-id="b82cd-117">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="b82cd-118">Die OWIN-Unterstützung von ASP.NET Core wird im Rahmen des `Microsoft.AspNetCore.Owin`-Pakets bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b82cd-118">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="b82cd-119">Sie können OWIN-Unterstützung in Ihrem Projekt ermöglichen, indem Sie dieses Paket installieren.</span><span class="sxs-lookup"><span data-stu-id="b82cd-119">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="b82cd-120">OWIN-Middleware stimmt mit der [OWIN-Spezifikation](https://owin.org/spec/spec/owin-1.0.0.html) überein, die eine `Func<IDictionary<string, object>, Task>`-Schnittstelle und das Festlegen spezifischer Schlüssel erfordert (wie z.B. `owin.ResponseBody`).</span><span class="sxs-lookup"><span data-stu-id="b82cd-120">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="b82cd-121">Die folgende einfache OWIN-Middleware zeigt „Hello World“ (Hallo Welt) an:</span><span class="sxs-lookup"><span data-stu-id="b82cd-121">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="b82cd-122">Die Beispielsignatur gibt ein `Task`-Objekt zurück und akzeptiert ein `IDictionary<string, object>`, wie OWIN es erfordert.</span><span class="sxs-lookup"><span data-stu-id="b82cd-122">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="b82cd-123">Der folgende Code veranschaulicht, wie Sie die `OwinHello`-Middleware (siehe oben) der ASP.NET Core-Pipeline mit der `UseOwin`-Erweiterungsmethode hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b82cd-123">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="b82cd-124">Sie können andere Aktionen konfigurieren, die in der OWIN-Pipeline durchgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="b82cd-124">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="b82cd-125">Antwortheader sollten nur vor dem ersten Schreiben in den Antwortstream modifiziert werden.</span><span class="sxs-lookup"><span data-stu-id="b82cd-125">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="b82cd-126">Mehrere Aufrufe von `UseOwin` werden aus Leistungsgründen nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="b82cd-126">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="b82cd-127">OWIN-Komponenten funktionieren am besten, wenn sie gruppiert werden.</span><span class="sxs-lookup"><span data-stu-id="b82cd-127">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="b82cd-128">Ausführen von ASP.NET Core auf einem auf OWIN basierten Server und Nutzen der WebSockets-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="b82cd-128">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="b82cd-129">Der Zugriff auf Features wie WebSockets ist ein weiteres Beispiel dafür, wie ASP.NET Core Features von auf OWIN basierten Servern nutzen kann.</span><span class="sxs-lookup"><span data-stu-id="b82cd-129">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="b82cd-130">Der .NET OWIN-Webserver, der im vorherigen Beispiel verwendet wurde, verfügt über integrierte Unterstützung für WebSockets. Dies kann von einer ASP.NET Core-Anwendung genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="b82cd-130">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="b82cd-131">Das unten stehende Beispiel zeigt eine einfache Web-App, die WebSockets unterstützt und alles zurück gibt, was über WebSockets an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="b82cd-131">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="b82cd-132">OWIN-Umgebung</span><span class="sxs-lookup"><span data-stu-id="b82cd-132">OWIN environment</span></span>

<span data-ttu-id="b82cd-133">Sie können mit `HttpContext` eine OWIN-Umgebung erstellen.</span><span class="sxs-lookup"><span data-stu-id="b82cd-133">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="b82cd-134">OWIN-Schlüssel</span><span class="sxs-lookup"><span data-stu-id="b82cd-134">OWIN keys</span></span>

<span data-ttu-id="b82cd-135">OWIN ist von einem `IDictionary<string,object>`-Objekt abhängig, um Informationen innerhalb eines Austauschs einer HTTP-Anforderung und einer Antwort weiterzugeben.</span><span class="sxs-lookup"><span data-stu-id="b82cd-135">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="b82cd-136">ASP.NET Core implementiert die unten aufgelisteten Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="b82cd-136">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="b82cd-137">Weitere Informationen finden Sie in den [Hauptspezifikationen und Erweiterungen](https://owin.org/#spec) und in den [OWIN Key Guidelines and Common Keys (Wichtigste Richtlinien und gängige Schlüsse von OWIN)](https://owin.org/spec/spec/CommonKeys.html).</span><span class="sxs-lookup"><span data-stu-id="b82cd-137">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="b82cd-138">Anforderungsdaten (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="b82cd-138">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="b82cd-139">Key</span><span class="sxs-lookup"><span data-stu-id="b82cd-139">Key</span></span>               | <span data-ttu-id="b82cd-140">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="b82cd-140">Value (type)</span></span> | <span data-ttu-id="b82cd-141">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b82cd-141">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b82cd-142">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="b82cd-142">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="b82cd-143">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="b82cd-143">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="b82cd-144">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="b82cd-144">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="b82cd-145">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="b82cd-145">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="b82cd-146">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="b82cd-146">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="b82cd-147">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="b82cd-147">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="b82cd-148">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="b82cd-148">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="b82cd-149">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="b82cd-149">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="b82cd-150">Anforderungsdaten (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="b82cd-150">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="b82cd-151">Key</span><span class="sxs-lookup"><span data-stu-id="b82cd-151">Key</span></span>               | <span data-ttu-id="b82cd-152">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="b82cd-152">Value (type)</span></span> | <span data-ttu-id="b82cd-153">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b82cd-153">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b82cd-154">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="b82cd-154">owin.RequestId</span></span> | `String` | <span data-ttu-id="b82cd-155">Optional</span><span class="sxs-lookup"><span data-stu-id="b82cd-155">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="b82cd-156">Antwortdaten (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="b82cd-156">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="b82cd-157">Key</span><span class="sxs-lookup"><span data-stu-id="b82cd-157">Key</span></span>               | <span data-ttu-id="b82cd-158">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="b82cd-158">Value (type)</span></span> | <span data-ttu-id="b82cd-159">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b82cd-159">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b82cd-160">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="b82cd-160">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="b82cd-161">Optional</span><span class="sxs-lookup"><span data-stu-id="b82cd-161">Optional</span></span> |
| <span data-ttu-id="b82cd-162">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="b82cd-162">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="b82cd-163">Optional</span><span class="sxs-lookup"><span data-stu-id="b82cd-163">Optional</span></span> |
| <span data-ttu-id="b82cd-164">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="b82cd-164">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="b82cd-165">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="b82cd-165">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="b82cd-166">Andere Daten (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="b82cd-166">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="b82cd-167">Key</span><span class="sxs-lookup"><span data-stu-id="b82cd-167">Key</span></span>               | <span data-ttu-id="b82cd-168">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="b82cd-168">Value (type)</span></span> | <span data-ttu-id="b82cd-169">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b82cd-169">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b82cd-170">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="b82cd-170">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="b82cd-171">owin.Version</span><span class="sxs-lookup"><span data-stu-id="b82cd-171">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="b82cd-172">Gängige Schlüssel</span><span class="sxs-lookup"><span data-stu-id="b82cd-172">Common keys</span></span>

| <span data-ttu-id="b82cd-173">Key</span><span class="sxs-lookup"><span data-stu-id="b82cd-173">Key</span></span>               | <span data-ttu-id="b82cd-174">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="b82cd-174">Value (type)</span></span> | <span data-ttu-id="b82cd-175">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b82cd-175">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b82cd-176">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="b82cd-176">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="b82cd-177">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="b82cd-177">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="b82cd-178">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="b82cd-178">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="b82cd-179">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="b82cd-179">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="b82cd-180">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="b82cd-180">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="b82cd-181">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="b82cd-181">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="b82cd-182">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="b82cd-182">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="b82cd-183">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="b82cd-183">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="b82cd-184">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="b82cd-184">SendFiles v0.3.0</span></span>

| <span data-ttu-id="b82cd-185">Key</span><span class="sxs-lookup"><span data-stu-id="b82cd-185">Key</span></span>               | <span data-ttu-id="b82cd-186">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="b82cd-186">Value (type)</span></span> | <span data-ttu-id="b82cd-187">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b82cd-187">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b82cd-188">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="b82cd-188">sendfile.SendAsync</span></span> | <span data-ttu-id="b82cd-189">Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b82cd-189">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="b82cd-190">Pro Anforderung</span><span class="sxs-lookup"><span data-stu-id="b82cd-190">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="b82cd-191">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="b82cd-191">Opaque v0.3.0</span></span>

| <span data-ttu-id="b82cd-192">Key</span><span class="sxs-lookup"><span data-stu-id="b82cd-192">Key</span></span>               | <span data-ttu-id="b82cd-193">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="b82cd-193">Value (type)</span></span> | <span data-ttu-id="b82cd-194">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b82cd-194">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b82cd-195">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="b82cd-195">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="b82cd-196">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="b82cd-196">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="b82cd-197">Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b82cd-197">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="b82cd-198">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="b82cd-198">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="b82cd-199">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="b82cd-199">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="b82cd-200">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="b82cd-200">WebSocket v0.3.0</span></span>

| <span data-ttu-id="b82cd-201">Key</span><span class="sxs-lookup"><span data-stu-id="b82cd-201">Key</span></span>               | <span data-ttu-id="b82cd-202">Wert (Typ)</span><span class="sxs-lookup"><span data-stu-id="b82cd-202">Value (type)</span></span> | <span data-ttu-id="b82cd-203">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b82cd-203">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b82cd-204">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="b82cd-204">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="b82cd-205">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="b82cd-205">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="b82cd-206">Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b82cd-206">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="b82cd-207">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="b82cd-207">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="b82cd-208">n/v</span><span class="sxs-lookup"><span data-stu-id="b82cd-208">Non-spec</span></span> |
| <span data-ttu-id="b82cd-209">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="b82cd-209">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="b82cd-210">Siehe [RFC6455 Abschnitt 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2), Schritt 5.5</span><span class="sxs-lookup"><span data-stu-id="b82cd-210">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="b82cd-211">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="b82cd-211">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="b82cd-212">Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b82cd-212">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="b82cd-213">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="b82cd-213">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="b82cd-214">Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b82cd-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="b82cd-215">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="b82cd-215">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="b82cd-216">Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b82cd-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="b82cd-217">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="b82cd-217">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="b82cd-218">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="b82cd-218">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="b82cd-219">Optional</span><span class="sxs-lookup"><span data-stu-id="b82cd-219">Optional</span></span> |
| <span data-ttu-id="b82cd-220">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="b82cd-220">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="b82cd-221">Optional</span><span class="sxs-lookup"><span data-stu-id="b82cd-221">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="b82cd-222">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b82cd-222">Additional resources</span></span>

* [<span data-ttu-id="b82cd-223">Middleware</span><span class="sxs-lookup"><span data-stu-id="b82cd-223">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="b82cd-224">Server</span><span class="sxs-lookup"><span data-stu-id="b82cd-224">Servers</span></span>](xref:fundamentals/servers/index)
