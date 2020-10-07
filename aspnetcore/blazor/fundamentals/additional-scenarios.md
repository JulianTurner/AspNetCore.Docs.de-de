---
title: Hostingmodellkonfiguration für ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erhalten Sie Informationen zu zusätzlichen Szenarios für die Blazor-Hostingmodellkonfiguration in ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/12/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: 236d95e54b772ea522911421084ec0d9022c45ff
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424138"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a><span data-ttu-id="5ec1b-103">Hostingmodellkonfiguration für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="5ec1b-103">ASP.NET Core Blazor hosting model configuration</span></span>

<span data-ttu-id="5ec1b-104">Von [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5ec1b-104">By [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5ec1b-105">Dieser Artikel behandelt die Hostingmodellkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-105">This article covers hosting model configuration.</span></span>

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a><span data-ttu-id="5ec1b-106">Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5ec1b-106">SignalR cross-origin negotiation for authentication</span></span>

<span data-ttu-id="5ec1b-107">*Dieser Abschnitt gilt für Blazor WebAssembly.*</span><span class="sxs-lookup"><span data-stu-id="5ec1b-107">*This section applies to Blazor WebAssembly.*</span></span>

<span data-ttu-id="5ec1b-108">So konfigurieren Sie den zugrunde liegenden SignalR-Client zum Senden von Anmeldeinformationen (z. B. cookie oder HTTP-Authentifizierungsheader):</span><span class="sxs-lookup"><span data-stu-id="5ec1b-108">To configure SignalR's underlying client to send credentials, such as cookies or HTTP authentication headers:</span></span>

* <span data-ttu-id="5ec1b-109">Verwenden Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>, um <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> auf ursprungsübergreifende [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)-Anforderungen festzulegen:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-109">Use <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> to set <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> on cross-origin [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch) requests:</span></span>

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

* <span data-ttu-id="5ec1b-110">Weisen Sie der <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>-Eigenschaft die <xref:System.Net.Http.HttpMessageHandler>-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-110">Assign the <xref:System.Net.Http.HttpMessageHandler> to the <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory> option:</span></span>

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

<span data-ttu-id="5ec1b-111">Weitere Informationen finden Sie unter <xref:signalr/configuration#configure-additional-options>.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-111">For more information, see <xref:signalr/configuration#configure-additional-options>.</span></span>

## <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="5ec1b-112">Reflektieren des Verbindungszustands auf der Benutzeroberfläche</span><span class="sxs-lookup"><span data-stu-id="5ec1b-112">Reflect the connection state in the UI</span></span>

<span data-ttu-id="5ec1b-113">*Dieser Abschnitt gilt für Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="5ec1b-113">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5ec1b-114">Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-114">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="5ec1b-115">Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-115">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="5ec1b-116">Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` auf der Razor-Seite von `_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-116">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the `_Host.cshtml` Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="5ec1b-117">Fügen Sie dem Stylesheet (`wwwroot/css/app.css` oder `wwwroot/css/site.css`) der App Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-117">Add the following to the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`):</span></span>

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

<span data-ttu-id="5ec1b-118">In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-118">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="5ec1b-119">CSS-Klasse</span><span class="sxs-lookup"><span data-stu-id="5ec1b-119">CSS class</span></span>                       | <span data-ttu-id="5ec1b-120">Steht für&hellip;</span><span class="sxs-lookup"><span data-stu-id="5ec1b-120">Indicates&hellip;</span></span> |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | <span data-ttu-id="5ec1b-121">Die Verbindung wurde getrennt.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-121">A lost connection.</span></span> <span data-ttu-id="5ec1b-122">Der Client versucht, die Verbindung wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-122">The client is attempting to reconnect.</span></span> <span data-ttu-id="5ec1b-123">Die modale Seite wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-123">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="5ec1b-124">Auf dem Server wird eine aktive Verbindung wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-124">An active connection is re-established to the server.</span></span> <span data-ttu-id="5ec1b-125">Die modale Seite wird ausgeblendet.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-125">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="5ec1b-126">Die Wiederherstellung der Verbindung ist wahrscheinlich aufgrund eines Netzwerkfehlers fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-126">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="5ec1b-127">Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-127">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="5ec1b-128">Die Wiederherstellung der Verbindung wurde abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-128">Reconnection rejected.</span></span> <span data-ttu-id="5ec1b-129">Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-129">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="5ec1b-130">Rufen Sie `location.reload()` auf, um die App neu zu laden.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-130">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="5ec1b-131">Dieser Verbindungszustand kann aus folgenden Gründen auftreten:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-131">This connection state may result when:</span></span><ul><li><span data-ttu-id="5ec1b-132">Aufgetretener Absturz auf der serverseitigen Verbindung.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-132">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="5ec1b-133">Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-133">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="5ec1b-134">Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-134">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="5ec1b-135">Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-135">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

## <a name="render-mode"></a><span data-ttu-id="5ec1b-136">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="5ec1b-136">Render mode</span></span>

<span data-ttu-id="5ec1b-137">*Dieser Abschnitt gilt für Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="5ec1b-137">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5ec1b-138">Blazor Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-138">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="5ec1b-139">Diese Einrichtung erfolgt auf der Razor-Seite von `_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-139">This is set up in the `_Host.cshtml` Razor page:</span></span>

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="5ec1b-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-140"><xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="5ec1b-141">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="5ec1b-141">Is prerendered into the page.</span></span>
* <span data-ttu-id="5ec1b-142">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-142">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| <span data-ttu-id="5ec1b-143">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="5ec1b-143">Render mode</span></span> | <span data-ttu-id="5ec1b-144">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="5ec1b-144">Description</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="5ec1b-145">Rendert die Komponente in statisches HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-145">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="5ec1b-146">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-146">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="5ec1b-147">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-147">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="5ec1b-148">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-148">Output from the component isn't included.</span></span> <span data-ttu-id="5ec1b-149">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-149">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="5ec1b-150">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-150">Renders the component into static HTML.</span></span> |

<span data-ttu-id="5ec1b-151">Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-151">Rendering server components from a static HTML page isn't supported.</span></span>

## <a name="initialize-the-no-locblazor-circuit"></a><span data-ttu-id="5ec1b-152">Initialisieren der Blazor-Verbindung</span><span class="sxs-lookup"><span data-stu-id="5ec1b-152">Initialize the Blazor circuit</span></span>

<span data-ttu-id="5ec1b-153">*Dieser Abschnitt gilt für Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="5ec1b-153">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5ec1b-154">Konfigurieren Sie den manuellen Start der [SignalR-Verbindung](xref:blazor/hosting-models#circuits) einer Blazor Server-App in der Datei `Pages/_Host.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-154">Configure the manual start of a Blazor Server app's [SignalR circuit](xref:blazor/hosting-models#circuits) in the `Pages/_Host.cshtml` file:</span></span>

* <span data-ttu-id="5ec1b-155">Fügen Sie ein `autostart="false"`-Attribut zum `<script>`-Tag für das `blazor.server.js`-Skript hinzu.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-155">Add an `autostart="false"` attribute to the `<script>` tag for the `blazor.server.js` script.</span></span>
* <span data-ttu-id="5ec1b-156">Fügen Sie ein Skript, das `Blazor.start` aufruft, nach dem Tag `blazor.server.js` des Skripts und innerhalb des schließenden Tags `</body>` ein.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-156">Place a script that calls `Blazor.start` after the `blazor.server.js` script's tag and inside the closing `</body>` tag.</span></span>

<span data-ttu-id="5ec1b-157">Wenn `autostart` deaktiviert ist, funktionieren alle Aspekte der App, die nicht von der Verbindung abhängen, normal.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-157">When `autostart` is disabled, any aspect of the app that doesn't depend on the circuit works normally.</span></span> <span data-ttu-id="5ec1b-158">So ist beispielsweise das clientseitige Routing betriebsbereit.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-158">For example, client-side routing is operational.</span></span> <span data-ttu-id="5ec1b-159">Aspekte, die von der Verbindung abhängen, sind jedoch erst betriebsbereit, nachdem `Blazor.start` aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-159">However, any aspect that depends on the circuit isn't operational until `Blazor.start` is called.</span></span> <span data-ttu-id="5ec1b-160">Das App-Verhalten ist ohne eine bestehende Verbindung unvorhersehbar.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-160">App behavior is unpredictable without an established circuit.</span></span> <span data-ttu-id="5ec1b-161">Komponentenmethoden können beispielsweise nicht ausgeführt werden, solange die Verbindung getrennt ist.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-161">For example, component methods fail to execute while the circuit is disconnected.</span></span>

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a><span data-ttu-id="5ec1b-162">Initialisieren von Blazor, wenn das Dokument bereit ist</span><span class="sxs-lookup"><span data-stu-id="5ec1b-162">Initialize Blazor when the document is ready</span></span>

<span data-ttu-id="5ec1b-163">So initialisieren Sie die Blazor-App, wenn das Dokument bereit ist:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-163">To initialize the Blazor app when the document is ready:</span></span>

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a><span data-ttu-id="5ec1b-164">Verketten mit dem `Promise`, der sich aus einem manuellen Start ergibt</span><span class="sxs-lookup"><span data-stu-id="5ec1b-164">Chain to the `Promise` that results from a manual start</span></span>

<span data-ttu-id="5ec1b-165">Wenn Sie weitere Aufgaben wie etwa eine JS-Interop-Initialisierung ausführen möchten, verwenden Sie `then` für die Verkettung mit dem `Promise`, der sich aus dem manuellen Start der Blazor-App ergibt:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-165">To perform additional tasks, such as JS interop initialization, use `then` to chain to the `Promise` that results from a manual Blazor app start:</span></span>

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

### <a name="configure-the-no-locsignalr-client"></a><span data-ttu-id="5ec1b-166">Konfigurieren des SignalR-Clients</span><span class="sxs-lookup"><span data-stu-id="5ec1b-166">Configure the SignalR client</span></span>

#### <a name="logging"></a><span data-ttu-id="5ec1b-167">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="5ec1b-167">Logging</span></span>

<span data-ttu-id="5ec1b-168">Zur Konfiguration der SignalR-Clientprotokollierung wird ein Konfigurationsobjekt (`configureSignalR`) übergeben, das `configureLogging` mit der Protokollebene im Client-Generator aufruft.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-168">To configure SignalR client logging, pass in a configuration object (`configureSignalR`) that calls `configureLogging` with the log level on the client builder:</span></span>

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

<span data-ttu-id="5ec1b-169">Im vorherigen Beispiel entspricht `information` der Protokollebene <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-169">In the preceding example, `information` is equivalent to a log level of <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.</span></span>

### <a name="modify-the-reconnection-handler"></a><span data-ttu-id="5ec1b-170">Ändern des Handlers für die Wiederherstellung einer Verbindung</span><span class="sxs-lookup"><span data-stu-id="5ec1b-170">Modify the reconnection handler</span></span>

<span data-ttu-id="5ec1b-171">Die Verbindungsereignisse des Handlers für die Wiederherstellung einer Verbindung können geändert werden, um benutzerdefinierte Verhaltensweisen zu erzeugen, z. B. für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-171">The reconnection handler's circuit connection events can be modified for custom behaviors, such as:</span></span>

* <span data-ttu-id="5ec1b-172">Benachrichtigung an einen Benutzer, wenn die Verbindung unterbrochen wird</span><span class="sxs-lookup"><span data-stu-id="5ec1b-172">To notify the user if the connection is dropped.</span></span>
* <span data-ttu-id="5ec1b-173">Ausführen der Protokollierung (vom Client), wenn eine Verbindung besteht</span><span class="sxs-lookup"><span data-stu-id="5ec1b-173">To perform logging (from the client) when a circuit is connected.</span></span>

<span data-ttu-id="5ec1b-174">Zum Ändern der Verbindungsereignisse registrieren Sie Rückrufe für die folgenden Verbindungsänderungen:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-174">To modify the connection events, register callbacks for the following connection changes:</span></span>

* <span data-ttu-id="5ec1b-175">Unterbrochene Verbindungen verwenden `onConnectionDown`.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-175">Dropped connections use `onConnectionDown`.</span></span>
* <span data-ttu-id="5ec1b-176">Hergestellte/wieder hergestellte Verbindungen verwenden `onConnectionUp`.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-176">Established/re-established connections use `onConnectionUp`.</span></span>

<span data-ttu-id="5ec1b-177">**Sowohl** `onConnectionDown` als auch `onConnectionUp` sind obligatorisch:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-177">**Both** `onConnectionDown` and `onConnectionUp` must be specified:</span></span>

```cshtml
<body>

    ...

    <script autostart="false" src="_framework/blazor.server.js"></script>
    <script>
      Blazor.start({
        reconnectionHandler: {
          onConnectionDown: (options, error) => console.error(error);
          onConnectionUp: () => console.log("Up, up, and away!");
        }
      });
    </script>
</body>
```

### <a name="adjust-the-reconnection-retry-count-and-interval"></a><span data-ttu-id="5ec1b-178">Passen Sie die Anzahl und das Intervall der Wiederholungsversuche zum erneuten Herstellen einer Verbindung an.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-178">Adjust the reconnection retry count and interval</span></span>

<span data-ttu-id="5ec1b-179">Legen Sie zum Anpassen der Anzahl und des Intervalls der Wiederholungsversuche zum erneuten Herstellen einer Verbindung die zulässige Anzahl von Wiederholungsversuchen (`maxRetries`) und den zulässigen Zeitraum in Millisekunden (`retryIntervalMilliseconds`) für jeden Versuch fest:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-179">To adjust the reconnection retry count and interval, set the number of retries (`maxRetries`) and period in milliseconds permitted for each retry attempt (`retryIntervalMilliseconds`):</span></span>

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

## <a name="hide-or-replace-the-reconnection-display"></a><span data-ttu-id="5ec1b-180">Ausblenden oder Ersetzen der Anzeige einer erneuten Verbindung</span><span class="sxs-lookup"><span data-stu-id="5ec1b-180">Hide or replace the reconnection display</span></span>

<span data-ttu-id="5ec1b-181">Legen Sie zum Ausblenden der Anzeige einer erneuten Verbindung das `_reconnectionDisplay` des Handlers für die erneuten Verbindung auf ein leeres Objekt fest (`{}` oder `new Object()`).</span><span class="sxs-lookup"><span data-stu-id="5ec1b-181">To hide the reconnection display, set the reconnection handler's `_reconnectionDisplay` to an empty object (`{}` or `new Object()`):</span></span>

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

<span data-ttu-id="5ec1b-182">Um die Anzeige einer erneuten Verbindung zu ersetzen, legen Sie im vorherigen Beispiel `_reconnectionDisplay` auf das anzuzeigende Element fest:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-182">To replace the reconnection display, set `_reconnectionDisplay` in the preceding example to the element for display:</span></span>

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

<span data-ttu-id="5ec1b-183">Der Platzhalter `{ELEMENT ID}` ist die ID des HTML-Elements, das angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-183">The placeholder `{ELEMENT ID}` is the ID of the HTML element to display.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5ec1b-184">Passen Sie die Verzögerung an, bevor die Anzeige einer erneuten Verbindung angezeigt wird, indem Sie die `transition-delay`-Eigenschaft im CSS (`wwwroot/css/site.css`) der App für das modale Element festlegen.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-184">Customize the delay before the reconnection display appears by setting the `transition-delay` property in the app's CSS (`wwwroot/css/site.css`) for the modal element.</span></span> <span data-ttu-id="5ec1b-185">Im folgenden Beispiel wird die Übergangsverzögerung von 500 ms (Standard) auf 1.000 ms (1 Sekunde) festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-185">The following example sets the transition delay from 500 ms (default) to 1,000 ms (1 second):</span></span>

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a><span data-ttu-id="5ec1b-186">Trennen der Blazor-Verbindung mit dem Client</span><span class="sxs-lookup"><span data-stu-id="5ec1b-186">Disconnect the Blazor circuit from the client</span></span>

<span data-ttu-id="5ec1b-187">Standardmäßig wird eine Blazor-Verbindung getrennt, wenn das [`unload`-Ereignis der Seite](https://developer.mozilla.org/docs/Web/API/Window/unload_event) ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-187">By default, a Blazor circuit is disconnected when the [`unload` page event](https://developer.mozilla.org/docs/Web/API/Window/unload_event) is triggered.</span></span> <span data-ttu-id="5ec1b-188">Um die Verbindung in anderen Szenarien auf dem Client zu trennen, rufen Sie `Blazor.disconnect` im entsprechenden Ereignishandler auf.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-188">To disconnect the circuit for other scenarios on the client, invoke `Blazor.disconnect` in the appropriate event handler.</span></span> <span data-ttu-id="5ec1b-189">Im folgenden Beispiel wird die Verbindung getrennt, wenn die Seite ausgeblendet wird ([`pagehide`-Ereignis](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span><span class="sxs-lookup"><span data-stu-id="5ec1b-189">In the following example, the circuit is disconnected when the page is hidden ([`pagehide` event](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):</span></span>

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

## <a name="influence-html-head-tag-elements"></a><span data-ttu-id="5ec1b-190">Beeinflussen von HTML-`<head>`-Tagelementen</span><span class="sxs-lookup"><span data-stu-id="5ec1b-190">Influence HTML `<head>` tag elements</span></span>

<span data-ttu-id="5ec1b-191">*Dieser Abschnitt gilt für das anstehende ASP.NET Core 5.0-Release von Blazor WebAssembly und Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="5ec1b-191">*This section applies to the upcoming ASP.NET Core 5.0 release of Blazor WebAssembly and Blazor Server.*</span></span>

<span data-ttu-id="5ec1b-192">Beim Rendern fügen die `Title`-, `Link`- und `Meta`-Komponenten Daten in den HTML-`<head>`-Tagelementen hinzu oder aktualisieren diese:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-192">When rendered, the `Title`, `Link`, and `Meta` components add or update data in the HTML `<head>` tag elements:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Web.Extensions.Head

<Title Value="{TITLE}" />
<Link href="{URL}" rel="stylesheet" />
<Meta content="{DESCRIPTION}" name="description" />
```

<span data-ttu-id="5ec1b-193">Im vorherigen Beispiel sind die Platzhalter für `{TITLE}`, `{URL}` und `{DESCRIPTION}` Zeichenfolgenwerte, Razor-Variablen oder Razor-Ausdrücke.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-193">In the preceding example, placeholders for `{TITLE}`, `{URL}`, and `{DESCRIPTION}` are string values, Razor variables, or Razor expressions.</span></span>

<span data-ttu-id="5ec1b-194">Die folgenden Merkmale gelten:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-194">The following characteristics apply:</span></span>

* <span data-ttu-id="5ec1b-195">Serverseitiges PreRendering wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-195">Server-side prerendering is supported.</span></span>
* <span data-ttu-id="5ec1b-196">Der `Value`-Parameter ist der einzige gültige Parameter für die `Title`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-196">The `Value` parameter is the only valid parameter for the `Title` component.</span></span>
* <span data-ttu-id="5ec1b-197">HTML-Attribute, die für die `Meta`- und `Link`-Komponenten bereitgestellt werden, werden in [zusätzlichen Attributen](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) erfasst und über das gerenderte HTML-Tag übergeben.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-197">HTML attributes provided to the `Meta` and `Link` components are captured in [additional attributes](xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters) and passed through to the rendered HTML tag.</span></span>
* <span data-ttu-id="5ec1b-198">Bei mehreren `Title`-Komponenten gibt der Titel der Seite den `Value` der zuletzt gerenderten `Title`-Komponente wieder.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-198">For multiple `Title` components, the title of the page reflects the `Value` of the last `Title` component rendered.</span></span>
* <span data-ttu-id="5ec1b-199">Wenn mehrere `Meta`- oder `Link`-Komponenten mit identischen Attributen enthalten sind, wird genau ein HTML-Tag pro `Meta`- oder `Link`-Komponente gerendert.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-199">If multiple `Meta` or `Link` components are included with identical attributes, there's exactly one HTML tag rendered per `Meta` or `Link` component.</span></span> <span data-ttu-id="5ec1b-200">Zwei `Meta`- oder `Link`-Komponenten können nicht auf dasselbe gerenderte HTML-Tag verweisen.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-200">Two `Meta` or `Link` components can't refer to the same rendered HTML tag.</span></span>
* <span data-ttu-id="5ec1b-201">Änderungen an den Parametern vorhandener `Meta`- oder `Link`-Komponenten werden in ihren gerenderten HTML-Tags widergespiegelt.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-201">Changes to the parameters of existing `Meta` or `Link` components are reflected in their rendered HTML tags.</span></span>
* <span data-ttu-id="5ec1b-202">Wenn die `Link`- oder `Meta`-Komponenten nicht mehr gerendert und somit vom Framework verworfen werden, werden ihre gerenderten HTML-Tags entfernt.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-202">When the `Link` or `Meta` components are no longer rendered and thus disposed by the framework, their rendered HTML tags are removed.</span></span>

<span data-ttu-id="5ec1b-203">Wenn eine der Frameworkkomponenten in einer untergeordneten Komponente verwendet wird, beeinflusst das gerenderte HTML-Tag jede andere untergeordnete Komponente der übergeordneten Komponente, solange die untergeordnete Komponente, die die Frameworkkomponente enthält, gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-203">When one of the framework components is used in a child component, the rendered HTML tag influences any other child component of the parent component as long as the child component containing the framework component is rendered.</span></span> <span data-ttu-id="5ec1b-204">Der Unterschied bei der Verwendung einer dieser Frameworkkomponenten in einer untergeordneten Komponente und dem Platzieren eines -HTML-Tags in `wwwroot/index.html` oder `Pages/_Host.cshtml` besteht darin, dass das gerenderte HTML-Tag einer Rahmenkomponente:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-204">The distinction between using the one of these framework components in a child component and placing a an HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:</span></span>

* <span data-ttu-id="5ec1b-205">Nach Anwendungszustand geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-205">Can be modified by application state.</span></span> <span data-ttu-id="5ec1b-206">Ein hartcodiertes HTML-Tag kann nicht nach Anwendungszustand geändert werden.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-206">A hard-coded HTML tag can't be modified by application state.</span></span>
* <span data-ttu-id="5ec1b-207">Aus dem HTML-`<head>` entfernt wird, wenn die übergeordnete Komponente nicht mehr gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-207">Is removed from the HTML `<head>` when the parent component is no longer rendered.</span></span>

::: moniker-end

## <a name="static-files"></a><span data-ttu-id="5ec1b-208">Statische Dateien</span><span class="sxs-lookup"><span data-stu-id="5ec1b-208">Static files</span></span>

<span data-ttu-id="5ec1b-209">*Dieser Abschnitt gilt für Blazor Server.*</span><span class="sxs-lookup"><span data-stu-id="5ec1b-209">*This section applies to Blazor Server.*</span></span>

<span data-ttu-id="5ec1b-210">Verwenden Sie **einen** der folgenden Ansätze, um zusätzliche Dateizuordnungen mit einem <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> zu erstellen oder andere <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-210">To create additional file mappings with a <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> or configure other <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>, use **one** of the following approaches.</span></span> <span data-ttu-id="5ec1b-211">In den folgenden Beispielen ist der Platzhalter `{EXTENSION}` die Dateierweiterung und der Platzhalter `{CONTENT TYPE}` der Inhaltstyp.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-211">In the following examples, the `{EXTENSION}` placeholder is the file extension, and the `{CONTENT TYPE}` placeholder is the content type.</span></span>

* <span data-ttu-id="5ec1b-212">Konfigurieren Sie Optionen über [Abhängigkeitsinjektion (Dependency Injection, DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) mithilfe von <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span><span class="sxs-lookup"><span data-stu-id="5ec1b-212">Configure options through [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) using <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  services.Configure<StaticFileOptions>(options =>
  {
      options.ContentTypeProvider = provider;
  });
  ```

  <span data-ttu-id="5ec1b-213">Da bei diesem Ansatz derselbe Dateianbieter konfiguriert wird, der für `blazor.server.js` verwendet wird, müssen Sie sicherstellen, dass die benutzerdefinierte Konfiguration nicht die Bereitstellung für `blazor.server.js` beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-213">Because this approach configures the same file provider used to serve `blazor.server.js`, make sure that your custom configuration doesn't interfere with serving `blazor.server.js`.</span></span> <span data-ttu-id="5ec1b-214">Entfernen Sie z. B. nicht die Zuordnung für JavaScript-Dateien, indem Sie den Anbieter mit `provider.Mappings.Remove(".js")` konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-214">For example, don't remove the mapping for JavaScript files by configuring the provider with `provider.Mappings.Remove(".js")`.</span></span>

* <span data-ttu-id="5ec1b-215">Verwenden Sie zwei Aufrufe von <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="5ec1b-215">Use two calls to <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):</span></span>
  * <span data-ttu-id="5ec1b-216">Konfigurieren Sie den benutzerdefinierten Dateianbieter im ersten Aufruf mit <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-216">Configure the custom file provider in the first call with <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.</span></span>
  * <span data-ttu-id="5ec1b-217">Die zweite Middleware verarbeitet `blazor.server.js`. Diese Datei verwendet die Standardkonfiguration der statischen Dateien, die vom Blazor-Framework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5ec1b-217">The second middleware serves `blazor.server.js`, which uses the default static files configuration provided by the Blazor framework.</span></span>

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

## <a name="additional-resources"></a><span data-ttu-id="5ec1b-218">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5ec1b-218">Additional resources</span></span>

* <xref:fundamentals/logging/index>
