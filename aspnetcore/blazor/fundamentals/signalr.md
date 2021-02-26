---
title: Leitfaden für ASP.NET Core Blazor SignalR
author: guardrex
description: Hier erfahren Sie, wie Sie Blazor SignalR-Verbindungen konfigurieren und verwalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/27/2021
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
uid: blazor/fundamentals/signalr
ms.openlocfilehash: 3198f45819020ca551617aa12a146f2b8a9a9f8e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279859"
---
# <a name="aspnet-core-blazor-signalr-guidance"></a><span data-ttu-id="cd251-103">Leitfaden für ASP.NET Core Blazor SignalR</span><span class="sxs-lookup"><span data-stu-id="cd251-103">ASP.NET Core Blazor SignalR guidance</span></span>

<span data-ttu-id="cd251-104">Allgemeine Anleitungen zur ASP.NET Core SignalR-Konfiguration finden Sie in den Artikeln im <xref:signalr/introduction>-Bereich der Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="cd251-104">For general guidance on ASP.NET Core SignalR configuration, see the topics in the <xref:signalr/introduction> area of the documentation.</span></span> <span data-ttu-id="cd251-105">Informationen zum Konfigurieren von SignalR, das [zu einer gehosteten Blazor WebAssembly-Lösung hinzugefügt wurde](xref:tutorials/signalr-blazor), finden Sie unter <xref:signalr/configuration#configure-server-options>.</span><span class="sxs-lookup"><span data-stu-id="cd251-105">To configure SignalR [added to a hosted Blazor WebAssembly solution](xref:tutorials/signalr-blazor), see <xref:signalr/configuration#configure-server-options>.</span></span>

## <a name="circuit-handler-options"></a><span data-ttu-id="cd251-106">Optionen für Verbindungshandler</span><span class="sxs-lookup"><span data-stu-id="cd251-106">Circuit handler options</span></span>

<span data-ttu-id="cd251-107">*Dieser Abschnitt gilt für Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="cd251-107">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="cd251-108">Konfigurieren Sie die Verbindung Blazor Server mit der in der folgenden Tabelle dargestellten <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="cd251-108">Configure the Blazor Server circuit with the <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> shown in the following table.</span></span>

| <span data-ttu-id="cd251-109">Option</span><span class="sxs-lookup"><span data-stu-id="cd251-109">Option</span></span> | <span data-ttu-id="cd251-110">Standard</span><span class="sxs-lookup"><span data-stu-id="cd251-110">Default</span></span> | <span data-ttu-id="cd251-111">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="cd251-111">Description</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors> | `false` | <span data-ttu-id="cd251-112">Senden Sie detaillierte Ausnahmemeldungen an JavaScript, wenn die unbehandelte Ausnahme in der Verbindung auftritt oder wenn ein .NET-Methodenaufruf über JS-Interop eine Ausnahme auslöst.</span><span class="sxs-lookup"><span data-stu-id="cd251-112">Send detailed exception messages to JavaScript when an unhandled exception happens on the circuit or when a .NET method invocation through JS interop results in an exception.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="cd251-113">100</span><span class="sxs-lookup"><span data-stu-id="cd251-113">100</span></span> | <span data-ttu-id="cd251-114">Maximale Anzahl der getrennten Circuits, die sich gleichzeitig im Arbeitsspeicher eines bestimmten Servers befinden</span><span class="sxs-lookup"><span data-stu-id="cd251-114">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="cd251-115">3 Minuten</span><span class="sxs-lookup"><span data-stu-id="cd251-115">3 minutes</span></span> | <span data-ttu-id="cd251-116">Maximale Zeitspanne, die ein getrennter Circuit im Arbeitsspeicher aufbewahrt wird, bevor er entfernt wird</span><span class="sxs-lookup"><span data-stu-id="cd251-116">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="cd251-117">1 Minute</span><span class="sxs-lookup"><span data-stu-id="cd251-117">1 minute</span></span> | <span data-ttu-id="cd251-118">Maximale Zeitspanne, die der Server wartet, bevor der Aufruf einer asynchronen JavaScript-Funktion durch ein Timeout beendet wird</span><span class="sxs-lookup"><span data-stu-id="cd251-118">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="cd251-119">10</span><span class="sxs-lookup"><span data-stu-id="cd251-119">10</span></span> | <span data-ttu-id="cd251-120">Maximale Anzahl nicht unbestätigter Renderbatches, die der Server zu einem bestimmten Zeitpunkt pro Circuit im Arbeitsspeicher aufbewahrt, um eine stabile Neuverbindung zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="cd251-120">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="cd251-121">Wenn die Obergrenze erreicht wird, generiert der Server keine neuen Renderbatches mehr, bis mindestens ein Batch vom Client bestätigt wurde.</span><span class="sxs-lookup"><span data-stu-id="cd251-121">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> |

<span data-ttu-id="cd251-122">Konfigurieren Sie die Optionen in `Startup.ConfigureServices` mit den Optionsdelegat zu <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span><span class="sxs-lookup"><span data-stu-id="cd251-122">Configure the options in `Startup.ConfigureServices` with an options delegate to <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>.</span></span> <span data-ttu-id="cd251-123">Im folgenden Beispiel sind die Standardoptionswerte in der nachstehenden Tabelle dargestellt:</span><span class="sxs-lookup"><span data-stu-id="cd251-123">The following example assigns the default option values shown in the preceding table:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor(options =>
{
    options.DetailedErrors = false;
    options.DisconnectedCircuitMaxRetained = 100;
    options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(3);
    options.JSInteropDefaultCallTimeout = TimeSpan.FromMinutes(1);
    options.MaxBufferedUnacknowledgedRenderBatches = 10;
});
```

<span data-ttu-id="cd251-124">Verwenden Sie <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> mit <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>, um <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext> zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="cd251-124">To configure the <xref:Microsoft.AspNetCore.SignalR.HubConnectionContext>, use <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> with <xref:Microsoft.Extensions.DependencyInjection.ServerSideBlazorBuilderExtensions.AddHubOptions%2A>.</span></span> <span data-ttu-id="cd251-125">Optionsbeschreibungen finden Sie unter <xref:signalr/configuration#configure-server-options>.</span><span class="sxs-lookup"><span data-stu-id="cd251-125">For option descriptions, see <xref:signalr/configuration#configure-server-options>.</span></span> <span data-ttu-id="cd251-126">Im folgenden Beispiel werden die Standardoptionswerte zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="cd251-126">The following example assigns the default option values:</span></span>

```csharp
using System;

...

services.AddServerSideBlazor()
    .AddHubOptions(options =>
    {
        options.ClientTimeoutInterval = TimeSpan.FromSeconds(30);
        options.EnableDetailedErrors = false;
        options.HandshakeTimeout = TimeSpan.FromSeconds(15);
        options.KeepAliveInterval = TimeSpan.FromSeconds(15);
        options.MaximumParallelInvocationsPerClient = 1;
        options.MaximumReceiveMessageSize = 32 * 1024;
        options.StreamBufferCapacity = 10;
    });
```

## <a name="signalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="cd251-127">Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cd251-127">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="cd251-128">*Dieser Abschnitt gilt für Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="cd251-128">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="cd251-129">So konfigurieren Sie den zugrunde liegenden SignalR-Client zum Senden von Anmeldeinformationen (z. B. cookie oder HTTP-Authentifizierungsheader):</span><span class="sxs-lookup"><span data-stu-id="cd251-129">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="cd251-130">Verwenden Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>, um <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> auf ursprungsübergreifende [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)-Anforderungen festzulegen:</span><span class="sxs-lookup"><span data-stu-id="cd251-130">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

  ```csharp
  public class IncludeRequestCredentialsMessageHandler : DelegatingHandler
  {
      protected override Task<HttpResponseMessage> SendAsync(
          HttpRequestMessage request, CancellationToken cancellationToken)
      {
          request.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
          return base.SendAsync(request, cancellationToken);
      }
  }
  ```

* <span data-ttu-id="cd251-131">Weisen Sie der <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>-Eigenschaft die <xref:System.Net.Http.HttpMessageHandler>-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="cd251-131">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="cd251-132">Weitere Informationen finden Sie unter <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="cd251-132">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="cd251-133">Reflektieren des Verbindungszustands auf der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="cd251-133">Reflect the connection state in the UI</span></span>

<span data-ttu-id="cd251-134">*Dieser Abschnitt gilt für Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="cd251-134">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="cd251-135">Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="cd251-135">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="cd251-136">Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen.</span><span class="sxs-lookup"><span data-stu-id="cd251-136">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="cd251-137">Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` auf der Razor-Seite von `_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="cd251-137">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="cd251-138">Fügen Sie dem Stylesheet (`wwwroot/css/app.css` oder `wwwroot/css/site.css`) der App Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="cd251-138">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="cd251-139">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="cd251-139">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="cd251-140">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="cd251-140">CSS class</span></span>                       | <span data-ttu-id="cd251-141">Steht für&hellip;</span><span class="sxs-lookup"><span data-stu-id="cd251-141">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="cd251-142">Die Verbindung wurde getrennt.</span><span class="sxs-lookup"><span data-stu-id="cd251-142">A lost connection.</span></span> <span data-ttu-id="cd251-143">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="cd251-143">The client is attempting to reconnect.</span></span> <span data-ttu-id="cd251-144">Die modale Seite wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="cd251-144">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="cd251-145">Auf dem Server wird eine aktive Verbindung wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="cd251-145">An active connection is re-established to the server.</span></span> <span data-ttu-id="cd251-146">Die modale Seite wird ausgeblendet.</span><span class="sxs-lookup"><span data-stu-id="cd251-146">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="cd251-147">Die Wiederherstellung der Verbindung ist wahrscheinlich aufgrund eines Netzwerkfehlers fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="cd251-147">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="cd251-148">Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen.</span><span class="sxs-lookup"><span data-stu-id="cd251-148">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="cd251-149">Die Wiederherstellung der Verbindung wurde abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="cd251-149">Reconnection rejected.</span></span> <span data-ttu-id="cd251-150">Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen.</span><span class="sxs-lookup"><span data-stu-id="cd251-150">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="cd251-151">Rufen Sie `location.reload()` auf, um die App neu zu laden.</span><span class="sxs-lookup"><span data-stu-id="cd251-151">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="cd251-152">Dieser Verbindungszustand kann aus folgenden Gründen auftreten:</span><span class="sxs-lookup"><span data-stu-id="cd251-152">This connection state may result when:</span></span><ul><li><span data-ttu-id="cd251-153">Aufgetretener Absturz auf der serverseitigen Verbindung.</span><span class="sxs-lookup"><span data-stu-id="cd251-153">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="cd251-154">Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat.</span><span class="sxs-lookup"><span data-stu-id="cd251-154">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="cd251-155">Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="cd251-155">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="cd251-156">Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.</span><span class="sxs-lookup"><span data-stu-id="cd251-156">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="cd251-157">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="cd251-157">Render mode</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cd251-158">*Dieser Abschnitt gilt für gehostete Blazor WebAssembly- und Blazor Server-Instanzen.*</span><span class="sxs-lookup"><span data-stu-id="cd251-158">*This section applies to hosted Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="cd251-159">Blazor-Apps werden standardmäßig so eingerichtet, dass sie die Benutzeroberfläche auf dem Server vorab rendern.</span><span class="sxs-lookup"><span data-stu-id="cd251-159">Blazor apps are set up by default to prerender the UI on the server.</span></span> <span data-ttu-id="cd251-160">Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="cd251-160">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="cd251-161">*Dieser Abschnitt gilt für Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="cd251-161">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="cd251-162">Blazor Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern.</span><span class="sxs-lookup"><span data-stu-id="cd251-162">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="cd251-163">Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="cd251-163">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

::: moniker-end

## <a name="initialize-the-blazor-circuit"></a><span data-ttu-id="cd251-164">Initialisieren der Blazor-Verbindung</span><span class="sxs-lookup"><span data-stu-id="cd251-164">Initialize the Blazor circuit</span></span>

<span data-ttu-id="cd251-165">*Dieser Abschnitt gilt für Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="cd251-165">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="cd251-166">Konfigurieren Sie den manuellen Start der [SignalR-Verbindung](xref:blazor/hosting-models#circuits) einer Blazor Server-App in der Datei `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="cd251-166">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="cd251-167">Fügen Sie ein `autostart="false"`-Attribut zum `<script>`-Tag für das `blazor.server.js`-Skript hinzu.</span><span class="sxs-lookup"><span data-stu-id="cd251-167">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="cd251-168">Fügen Sie ein Skript, das `Blazor.start` aufruft, nach dem Tag `blazor.server.js` des Skripts und innerhalb des schließenden Tags `</body>` ein.</span><span class="sxs-lookup"><span data-stu-id="cd251-168">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="cd251-169">Wenn `autostart` deaktiviert ist, funktionieren alle Aspekte der App, die nicht von der Verbindung abhängen, normal.</span><span class="sxs-lookup"><span data-stu-id="cd251-169">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="cd251-170">So ist beispielsweise das clientseitige Routing betriebsbereit.</span><span class="sxs-lookup"><span data-stu-id="cd251-170">For example, client-side routing is operational.</span></span> <span data-ttu-id="cd251-171">Aspekte, die von der Verbindung abhängen, sind jedoch erst betriebsbereit, nachdem `Blazor.start` aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="cd251-171">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="cd251-172">Das App-Verhalten ist ohne eine bestehende Verbindung unvorhersehbar.</span><span class="sxs-lookup"><span data-stu-id="cd251-172">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="cd251-173">Komponentenmethoden können beispielsweise nicht ausgeführt werden, solange die Verbindung getrennt ist.</span><span class="sxs-lookup"><span data-stu-id="cd251-173">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-blazor-when-the-document-is-ready"></a><span data-ttu-id="cd251-174">Initialisieren von Blazor, wenn das Dokument bereit ist</span><span class="sxs-lookup"><span data-stu-id="cd251-174">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="cd251-175">So initialisieren Sie die Blazor-App, wenn das Dokument bereit ist:</span><span class="sxs-lookup"><span data-stu-id="cd251-175">To initialize the Blazor app when the document is ready:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      document.addEventListener("DOMContentLoaded", function() {
        Blazor.start();
      });
    </script>
</body>
```

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="cd251-176">Verketten mit dem `Promise`, der sich aus einem manuellen Start ergibt</span><span class="sxs-lookup"><span data-stu-id="cd251-176">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="cd251-177">Wenn Sie weitere Aufgaben wie etwa eine JS-Interop-Initialisierung ausführen möchten, verwenden Sie `then` für die Verkettung mit dem `Promise`, der sich aus dem manuellen Start der Blazor-App ergibt:</span><span class="sxs-lookup"><span data-stu-id="cd251-177">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start().then(function () {
        ...
      });
    </script>
</body>
```

### <a name="configure-the-signalr-client"></a><span data-ttu-id="cd251-178">Konfigurieren des SignalR-Clients</span><span class="sxs-lookup"><span data-stu-id="cd251-178">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="cd251-179">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="cd251-179">Logging</span></span>

<span data-ttu-id="cd251-180">Zur Konfiguration der SignalR-Clientprotokollierung wird ein Konfigurationsobjekt (`configureSignalR`) übergeben, das `configureLogging` mit der Protokollebene im Client-Generator aufruft.</span><span class="sxs-lookup"><span data-stu-id="cd251-180">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        configureSignalR: function (builder) {
          builder.configureLogging("information");
        }
      });
    </script>
</body>
```

<span data-ttu-id="cd251-181">Im vorherigen Beispiel entspricht `information` der Protokollebene <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="cd251-181">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="cd251-182">Ändern des Handlers für die Wiederherstellung einer Verbindung</span><span class="sxs-lookup"><span data-stu-id="cd251-182">Modify the reconnection handler</span></span>

<span data-ttu-id="cd251-183">Die Verbindungsereignisse des Handlers für die Wiederherstellung einer Verbindung können geändert werden, um benutzerdefinierte Verhaltensweisen zu erzeugen, z. B. für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cd251-183">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="cd251-184">Benachrichtigung an einen Benutzer, wenn die Verbindung unterbrochen wird</span><span class="sxs-lookup"><span data-stu-id="cd251-184">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="cd251-185">Ausführen der Protokollierung (vom Client), wenn eine Verbindung besteht</span><span class="sxs-lookup"><span data-stu-id="cd251-185">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="cd251-186">Zum Ändern der Verbindungsereignisse registrieren Sie Rückrufe für die folgenden Verbindungsänderungen:</span><span class="sxs-lookup"><span data-stu-id="cd251-186">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="cd251-187">Unterbrochene Verbindungen verwenden `onConnectionDown`.</span><span class="sxs-lookup"><span data-stu-id="cd251-187">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="cd251-188">Hergestellte/wieder hergestellte Verbindungen verwenden `onConnectionUp`.</span><span class="sxs-lookup"><span data-stu-id="cd251-188">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="cd251-189">**Sowohl** `onConnectionDown` als auch `onConnectionUp` sind obligatorisch:</span><span class="sxs-lookup"><span data-stu-id="cd251-189">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error),
          onConnectionUp: () => console.log("Up, up, and away!")
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="cd251-190">Passen Sie die Anzahl und das Intervall der Wiederholungsversuche zum erneuten Herstellen einer Verbindung an.</span><span class="sxs-lookup"><span data-stu-id="cd251-190">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="cd251-191">Legen Sie zum Anpassen der Anzahl und des Intervalls der Wiederholungsversuche zum erneuten Herstellen einer Verbindung die zulässige Anzahl von Wiederholungsversuchen (`maxRetries`) und den zulässigen Zeitraum in Millisekunden (`retryIntervalMilliseconds`) für jeden Versuch fest:</span><span class="sxs-lookup"><span data-stu-id="cd251-191">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionOptions: {
          maxRetries: 3,
          retryIntervalMilliseconds: 2000
        }
      });
    </script>
</body>
```

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="cd251-192">Ausblenden oder Ersetzen der Anzeige einer erneuten Verbindung</span><span class="sxs-lookup"><span data-stu-id="cd251-192">Hide or replace the reconnection display</span></span>

<span data-ttu-id="cd251-193">Legen Sie zum Ausblenden der Anzeige einer erneuten Verbindung das `_reconnectionDisplay` des Handlers für die erneuten Verbindung auf ein leeres Objekt fest (`{}` oder `new Object()`).</span><span class="sxs-lookup"><span data-stu-id="cd251-193">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      window.addEventListener('beforeunload', function () {
        Blazor.defaultReconnectionHandler._reconnectionDisplay = {};
      });

      Blazor.start();
    </script>
</body>
```

<span data-ttu-id="cd251-194">Um die Anzeige einer erneuten Verbindung zu ersetzen, legen Sie im vorherigen Beispiel `_reconnectionDisplay` auf das anzuzeigende Element fest:</span><span class="sxs-lookup"><span data-stu-id="cd251-194">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="cd251-195">Der Platzhalter `{ELEMENT ID}` ist die ID des HTML-Elements, das angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="cd251-195">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cd251-196">Passen Sie die Verzögerung an, bevor die Anzeige einer erneuten Verbindung angezeigt wird, indem Sie die `transition-delay`-Eigenschaft im CSS (`wwwroot/css/site.css`) der App für das modale Element festlegen.</span><span class="sxs-lookup"><span data-stu-id="cd251-196">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="cd251-197">Im folgenden Beispiel wird die Übergangsverzögerung von 500 ms (Standard) auf 1.000 ms (1 Sekunde) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="cd251-197">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-blazor-circuit-from-the-client"></a><span data-ttu-id="cd251-198">Trennen der Blazor-Verbindung mit dem Client</span><span class="sxs-lookup"><span data-stu-id="cd251-198">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="cd251-199">Standardmäßig wird eine Blazor-Verbindung getrennt, wenn das [`unload`-Ereignis der Seite](https://developer.mozilla.org/docs/Web/API/Window/unload_event) ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="cd251-199">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="cd251-200">Um die Verbindung in anderen Szenarien auf dem Client zu trennen, rufen Sie `Blazor.disconnect` im entsprechenden Ereignishandler auf.</span><span class="sxs-lookup"><span data-stu-id="cd251-200">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="cd251-201">Im folgenden Beispiel wird die Verbindung getrennt, wenn die Seite ausgeblendet wird ([`pagehide`-Ereignis](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="cd251-201">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

THIS WILL BE MOVED TO ANOTHER TOPIC WHEN RE-ACTIVATED.

## Influence HTML `<head>` tag elements

*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*

When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.

The following characteristics apply:

* Server-side prerendering is supported.
* The `Value` parameter is the only valid parameter for the `Title` component.
* HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.
* For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.
* If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component. Two `Meta` or `Link` components can't refer to the same rendered HTML tag.
* Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.
* When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.

When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered. The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cd251-202">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cd251-202">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:signalr/configuration>
* <xref:blazor/security/server/threat-mitigation>
* [<span data-ttu-id="cd251-203">Blazor Server-Ereignisse zur Wiederherstellung von Verbindungen und zum Komponentenlebenszyklus</span><span class="sxs-lookup"><span data-stu-id="cd251-203">Blazor Server reconnection events and component lifecycle events</span></span>](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
