---
title: Hostingmodellkonfiguration für ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erhalten Sie Informationen zu zusätzlichen Szenarios für die Blazor-Hostingmodellkonfiguration in ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/fundamentals/additional-scenarios
ms.openlocfilehash: b7fc3710fe5ad1efba907edf98f590a42e2a83ae
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485874"
---
# <a name="aspnet-core-no-locblazor-hosting-model-configuration"></a>Hostingmodellkonfiguration für ASP.NET Core Blazor

Von [Daniel Roth](https://github.com/danroth27), [Mackinnon Buck](https://github.com/MackinnonBuck) und [Luke Latham](https://github.com/guardrex)

Dieser Artikel behandelt die Hostingmodellkonfiguration.

### <a name="no-locsignalr-cross-origin-negotiation-for-authentication"></a>Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung

*Dieser Abschnitt gilt für Blazor WebAssembly.*

So konfigurieren Sie den zugrunde liegenden SignalR-Client zum Senden von Anmeldeinformationen (z. B. cookie oder HTTP-Authentifizierungsheader):

* Verwenden Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>, um <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.BrowserRequestCredentials.Include> auf ursprungsübergreifende [`fetch`](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)-Anforderungen festzulegen:

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

* Weisen Sie der <xref:Microsoft.AspNetCore.Http.Connections.Client.HttpConnectionOptions.HttpMessageHandlerFactory>-Eigenschaft die <xref:System.Net.Http.HttpMessageHandler>-Klasse hinzu:

  ```csharp
  var connection = new HubConnectionBuilder()
      .WithUrl(new Uri("http://signalr.example.com"), options =>
      {
          options.HttpMessageHandlerFactory = innerHandler => 
              new IncludeRequestCredentialsMessageHandler { InnerHandler = innerHandler };
      }).Build();
  ```

Weitere Informationen finden Sie unter <xref:signalr/configuration#configure-additional-options>.

## <a name="reflect-the-connection-state-in-the-ui"></a>Reflektieren des Verbindungszustands auf der Benutzeroberfläche

*Dieser Abschnitt gilt für Blazor Server.*

Wenn der Client erkennt, dass keine Verbindung mehr besteht, wird dem Benutzer eine Standardbenutzeroberfläche angezeigt, während der Client versucht, eine neue Verbindung herzustellen. Wenn die Wiederherstellung der Verbindung fehlschlägt, wird dem Benutzer die Option angezeigt, es noch mal zu versuchen.

Wenn Sie die Benutzeroberfläche anpassen möchten, definieren Sie ein Element mit einer `id` von `components-reconnect-modal` im `<body>` auf der Razor-Seite von `_Host.cshtml`:

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

Fügen Sie dem Stylesheet (`wwwroot/css/app.css` oder `wwwroot/css/site.css`) der App Folgendes hinzu:

```css
#components-reconnect-modal {
    display: none;
}

#components-reconnect-modal.components-reconnect-show {
    display: block;
}
```

In der folgenden Tabelle werden die CSS-Klassen beschrieben, die auf das `components-reconnect-modal`-Element angewendet werden.

| CSS-Klasse                       | Steht für&hellip; |
| ------------------------------- | ----------------- |
| `components-reconnect-show`     | Die Verbindung wurde getrennt. Der Client versucht, die Verbindung wiederherzustellen. Die modale Seite wird angezeigt. |
| `components-reconnect-hide`     | Auf dem Server wird eine aktive Verbindung wiederhergestellt. Die modale Seite wird ausgeblendet. |
| `components-reconnect-failed`   | Die Wiederherstellung der Verbindung ist wahrscheinlich aufgrund eines Netzwerkfehlers fehlgeschlagen. Rufen Sie `window.Blazor.reconnect()` auf, um einen neuen Verbindungsversuch zu unternehmen. |
| `components-reconnect-rejected` | Die Wiederherstellung der Verbindung wurde abgelehnt. Der Server wurde zwar erreicht, jedoch hat dieser die Verbindung verweigert. Der Status des Benutzers auf dem Server wurde verworfen. Rufen Sie `location.reload()` auf, um die App neu zu laden. Dieser Verbindungszustand kann aus folgenden Gründen auftreten:<ul><li>Aufgetretener Absturz auf der serverseitigen Verbindung.</li><li>Der Client war lange nicht verbunden, sodass der Server den Benutzerstatus verworfen hat. Komponenteninstanzen, mit denen der Benutzer interagiert, werden verworfen.</li><li>Der Server wird neu gestartet, oder der Workerprozess der App wird wiederverwendet.</li></ul> |

## <a name="render-mode"></a>Rendermodus

::: moniker range=">= aspnetcore-5.0"

*Dieser Abschnitt gilt für gehostete Blazor WebAssembly- und Blazor Server-Instanzen.*

Blazor-Apps werden standardmäßig so eingerichtet, dass sie die Benutzeroberfläche auf dem Server vorab rendern. Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

*Dieser Abschnitt gilt für Blazor Server.*

Blazor Server-Apps werden standardmäßig eingerichtet, um die Benutzeroberfläche auf dem Server schon vor der Einrichtung der Clientverbindung auf dem Server zu rendern. Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

::: moniker-end

## <a name="initialize-the-no-locblazor-circuit"></a>Initialisieren der Blazor-Verbindung

*Dieser Abschnitt gilt für Blazor Server.*

Konfigurieren Sie den manuellen Start der [SignalR-Verbindung](xref:blazor/hosting-models#circuits) einer Blazor Server-App in der Datei `Pages/_Host.cshtml`:

* Fügen Sie ein `autostart="false"`-Attribut zum `<script>`-Tag für das `blazor.server.js`-Skript hinzu.
* Fügen Sie ein Skript, das `Blazor.start` aufruft, nach dem Tag `blazor.server.js` des Skripts und innerhalb des schließenden Tags `</body>` ein.

Wenn `autostart` deaktiviert ist, funktionieren alle Aspekte der App, die nicht von der Verbindung abhängen, normal. So ist beispielsweise das clientseitige Routing betriebsbereit. Aspekte, die von der Verbindung abhängen, sind jedoch erst betriebsbereit, nachdem `Blazor.start` aufgerufen wurde. Das App-Verhalten ist ohne eine bestehende Verbindung unvorhersehbar. Komponentenmethoden können beispielsweise nicht ausgeführt werden, solange die Verbindung getrennt ist.

### <a name="initialize-no-locblazor-when-the-document-is-ready"></a>Initialisieren von Blazor, wenn das Dokument bereit ist

So initialisieren Sie die Blazor-App, wenn das Dokument bereit ist:

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

### <a name="chain-to-the-promise-that-results-from-a-manual-start"></a>Verketten mit dem `Promise`, der sich aus einem manuellen Start ergibt

Wenn Sie weitere Aufgaben wie etwa eine JS-Interop-Initialisierung ausführen möchten, verwenden Sie `then` für die Verkettung mit dem `Promise`, der sich aus dem manuellen Start der Blazor-App ergibt:

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

### <a name="configure-the-no-locsignalr-client"></a>Konfigurieren des SignalR-Clients

#### <a name="logging"></a>Protokollierung

Zur Konfiguration der SignalR-Clientprotokollierung wird ein Konfigurationsobjekt (`configureSignalR`) übergeben, das `configureLogging` mit der Protokollebene im Client-Generator aufruft.

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

Im vorherigen Beispiel entspricht `information` der Protokollebene <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>.

### <a name="modify-the-reconnection-handler"></a>Ändern des Handlers für die Wiederherstellung einer Verbindung

Die Verbindungsereignisse des Handlers für die Wiederherstellung einer Verbindung können geändert werden, um benutzerdefinierte Verhaltensweisen zu erzeugen, z. B. für Folgendes:

* Benachrichtigung an einen Benutzer, wenn die Verbindung unterbrochen wird
* Ausführen der Protokollierung (vom Client), wenn eine Verbindung besteht

Zum Ändern der Verbindungsereignisse registrieren Sie Rückrufe für die folgenden Verbindungsänderungen:

* Unterbrochene Verbindungen verwenden `onConnectionDown`.
* Hergestellte/wieder hergestellte Verbindungen verwenden `onConnectionUp`.

**Sowohl** `onConnectionDown` als auch `onConnectionUp` sind obligatorisch:

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

### <a name="adjust-the-reconnection-retry-count-and-interval"></a>Passen Sie die Anzahl und das Intervall der Wiederholungsversuche zum erneuten Herstellen einer Verbindung an.

Legen Sie zum Anpassen der Anzahl und des Intervalls der Wiederholungsversuche zum erneuten Herstellen einer Verbindung die zulässige Anzahl von Wiederholungsversuchen (`maxRetries`) und den zulässigen Zeitraum in Millisekunden (`retryIntervalMilliseconds`) für jeden Versuch fest:

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

## <a name="hide-or-replace-the-reconnection-display"></a>Ausblenden oder Ersetzen der Anzeige einer erneuten Verbindung

Legen Sie zum Ausblenden der Anzeige einer erneuten Verbindung das `_reconnectionDisplay` des Handlers für die erneuten Verbindung auf ein leeres Objekt fest (`{}` oder `new Object()`).

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

Um die Anzeige einer erneuten Verbindung zu ersetzen, legen Sie im vorherigen Beispiel `_reconnectionDisplay` auf das anzuzeigende Element fest:

```javascript
Blazor.defaultReconnectionHandler._reconnectionDisplay = 
  document.getElementById("{ELEMENT ID}");
```

Der Platzhalter `{ELEMENT ID}` ist die ID des HTML-Elements, das angezeigt werden soll.

::: moniker range=">= aspnetcore-5.0"

Passen Sie die Verzögerung an, bevor die Anzeige einer erneuten Verbindung angezeigt wird, indem Sie die `transition-delay`-Eigenschaft im CSS (`wwwroot/css/site.css`) der App für das modale Element festlegen. Im folgenden Beispiel wird die Übergangsverzögerung von 500 ms (Standard) auf 1.000 ms (1 Sekunde) festgelegt:

```css
#components-reconnect-modal {
    transition: visibility 0s linear 1000ms;
}
```

## <a name="disconnect-the-no-locblazor-circuit-from-the-client"></a>Trennen der Blazor-Verbindung mit dem Client

Standardmäßig wird eine Blazor-Verbindung getrennt, wenn das [`unload`-Ereignis der Seite](https://developer.mozilla.org/docs/Web/API/Window/unload_event) ausgelöst wird. Um die Verbindung in anderen Szenarien auf dem Client zu trennen, rufen Sie `Blazor.disconnect` im entsprechenden Ereignishandler auf. Im folgenden Beispiel wird die Verbindung getrennt, wenn die Seite ausgeblendet wird ([`pagehide`-Ereignis](https://developer.mozilla.org/docs/Web/API/Window/pagehide_event)):

```javascript
window.addEventListener('pagehide', () => {
  Blazor.disconnect();
});
```

<!-- HOLD for reactivation at 5x

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

## <a name="static-files"></a>Statische Dateien

*Dieser Abschnitt gilt für Blazor Server.*

Verwenden Sie **einen** der folgenden Ansätze, um zusätzliche Dateizuordnungen mit einem <xref:Microsoft.AspNetCore.StaticFiles.FileExtensionContentTypeProvider> zu erstellen oder andere <xref:Microsoft.AspNetCore.Builder.StaticFileOptions> zu konfigurieren. In den folgenden Beispielen ist der Platzhalter `{EXTENSION}` die Dateierweiterung und der Platzhalter `{CONTENT TYPE}` der Inhaltstyp.

* Konfigurieren Sie Optionen über [Abhängigkeitsinjektion (Dependency Injection, DI)](xref:blazor/fundamentals/dependency-injection) in `Startup.ConfigureServices` (`Startup.cs`) mithilfe von <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>:

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

  Da bei diesem Ansatz derselbe Dateianbieter konfiguriert wird, der für `blazor.server.js` verwendet wird, müssen Sie sicherstellen, dass die benutzerdefinierte Konfiguration nicht die Bereitstellung für `blazor.server.js` beeinträchtigt. Entfernen Sie z. B. nicht die Zuordnung für JavaScript-Dateien, indem Sie den Anbieter mit `provider.Mappings.Remove(".js")` konfigurieren.

* Verwenden Sie zwei Aufrufe von <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> in `Startup.Configure` (`Startup.cs`):
  * Konfigurieren Sie den benutzerdefinierten Dateianbieter im ersten Aufruf mit <xref:Microsoft.AspNetCore.Builder.StaticFileOptions>.
  * Die zweite Middleware verarbeitet `blazor.server.js`. Diese Datei verwendet die Standardkonfiguration der statischen Dateien, die vom Blazor-Framework bereitgestellt wird.

  ```csharp
  using Microsoft.AspNetCore.StaticFiles;

  ...

  var provider = new FileExtensionContentTypeProvider();
  provider.Mappings["{EXTENSION}"] = "{CONTENT TYPE}";

  app.UseStaticFiles(new StaticFileOptions { ContentTypeProvider = provider });
  app.UseStaticFiles();
  ```

* Sie können eine Störung der Verarbeitung von `_framework/blazor.server.js` verhindern, indem Sie mit <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> eine benutzerdefinierte Middleware für statische Dateien ausführen:

  ```csharp
  app.MapWhen(ctx => !ctx.Request.Path
      .StartsWithSegments("_framework/blazor.server.js", 
          subApp => subApp.UseStaticFiles(new StaticFileOptions(){ ... })));
  ```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/logging/index>
* [Blazor Server-Ereignisse zur Wiederherstellung von Verbindungen und zum Komponentenlebenszyklus](xref:blazor/components/lifecycle#blazor-server-reconnection-events)
