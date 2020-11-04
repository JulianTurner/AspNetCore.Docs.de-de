---
title: Globalisierung und Lokalisierung in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie Razor-Komponenten Benutzern mit verschiedenen Kulturen und Sprachen zur Verfügung stellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/globalization-localization
ms.openlocfilehash: 52810cb5a5961ffe932a7f5ac2a3a03033781cc9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678489"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a>Globalisierung und Lokalisierung in ASP.NET Core Blazor

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten können Benutzern mit verschiedenen Kulturen und Sprachen zur Verfügung gestellt werden. Die folgenden Szenarios zur Globalisierung und Lokalisierung von .NET sind verfügbar:

* .NET-Ressourcensystem
* Kulturspezifische Zahlen- und Datumsformatierung

Derzeit werden Lokalisierungsszenarios für ASP.NET Core eingeschränkt unterstützt:

* <xref:Microsoft.Extensions.Localization.IStringLocalizer> und <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> werden in Blazor-Apps *unterstützt*.
* <xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> und die Lokalisierung von Datenanmerkungen sind ASP.NET Core MVC-Szenarios und **werden nicht in Blazor-Apps unterstützt**.

Weitere Informationen finden Sie unter <xref:fundamentals/localization>.

## <a name="globalization"></a>Globalisierung

Die [`@bind`](xref:mvc/views/razor#bind)-Funktionalität von Blazor führt die Formatierung durch und analysiert Werte, um die Anzeige an die Kultur des jeweiligen Benutzers anzupassen.

Der Zugriff auf die aktuelle Kultur kann über die <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>-Eigenschaft erfolgen.

<xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> wird für die folgenden Feldtypen verwendet (`<input type="{TYPE}" />`):

* `date`
* `number`

Diese Feldtypen:

* werden basierend auf ihren entsprechenden browserbasierten Formatierungsregeln angezeigt.
* können keinen Freiformtext enthalten.
* bieten Benutzerinteraktionsmerkmale basierend auf der Implementierung des Browsers.

Die folgenden Feldtypen verfügen über spezifische Formatierungsanforderungen und werden derzeit nicht von Blazor unterstützt, weil sie von keinem der gängigen Browser unterstützt werden:

* `datetime-local`
* `month`
* `week`

[`@bind`](xref:mvc/views/razor#bind) unterstützt den Parameter `@bind:culture`, um eine Klasse <xref:System.Globalization.CultureInfo?displayProperty=fullName> für das Analysieren und Formatieren von Werten bereitzustellen. Vom Festlegen einer Kultur wird abgeraten, wenn die Feldtypen `date` und `number` verwendet werden. `date` und `number` verfügen über integrierte Blazor-Unterstützung, die die erforderliche Kultur bereitstellt.

## <a name="localization"></a>Lokalisierung

### Blazor WebAssembly

Blazor WebAssembly-Apps legen die Kultur anhand der [Spracheinstellungen](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages) des Benutzers fest.

Legen Sie in `Program.Main` <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> und <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> fest, um die Kultur explizit zu konfigurieren.

::: moniker range=">= aspnetcore-5.0"

Blazor WebAssembly enthält standardmäßig minimale Globalisierungsressourcen, die zum Anzeigen von Werten wie Datums- und Währungsangaben in der Kultur des Benutzers erforderlich sind. Bei Anwendungen, die eine dynamische Änderung der Kultur unterstützen müssen, sollte `BlazorWebAssemblyLoadAllGlobalizationData` in der Projektdatei konfiguriert werden:

```xml
<PropertyGroup>
  <BlazorWebAssemblyLoadAllGlobalizationData>true</BlazorWebAssemblyLoadAllGlobalizationData>
</PropertyGroup>
```

Blazor WebAssembly kann auch so konfiguriert werden, dass der Start mithilfe einer bestimmten Anwendungskultur erfolgt, indem Optionen an `Blazor.start` übergeben werden. Das folgende Beispiel zeigt eine App, die für den Start mit der Kultur `en-GB` konfiguriert wurde:

```html
<script src="_framework/blazor.webassembly.js" autostart="false"></script>
<script>
  Blazor.start({
    applicationCulture: 'en-GB'
  });
</script>
```

Der Wert von `applicationCulture` muss dem [Sprachentagformat BCP-47](https://tools.ietf.org/html/bcp47) entsprechen.

Wenn für die App keine Lokalisierung erforderlich ist, können Sie die App so konfigurieren, dass die invariante Kultur unterstützt wird, die auf der `en-US`-Kultur basiert:

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Mit der IL-Linkerkonfiguration (Intermediate Language, Zwischensprache) für Blazor WebAssembly-Apps werden mit Ausnahme von explizit angeforderten Gebietsschemas alle Internationalisierungsinformationen standardmäßig entfernt. Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.

::: moniker-end

Auch wenn für die meisten Benutzer die standardmäßig von Blazor ausgewählte Kultur möglicherweise ausreichend ist, ziehen Sie in Betracht, Benutzern die Möglichkeit zu geben, ihr bevorzugtes Gebietsschema anzugeben. Eine Blazor WebAssembly-Beispiel-App mit Kulturauswahl finden Sie in [`LocSample`](https://github.com/pranavkm/LocSample), der Beispiel-App für die Lokalisierung.

### Blazor Server

Blazor Server-Apps werden mit [Lokalisierungsmiddleware](xref:fundamentals/localization#localization-middleware) lokalisiert. Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der App anfordern.

Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:

* [Cookies](#cookies)
* [Bereitstellen einer Benutzeroberfläche zum Auswählen der Kultur](#provide-ui-to-choose-the-culture)

Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.

#### <a name="no-loccookies"></a>Cookie

Ein cookie für die Lokalisierungskultur kann die Kultur des Benutzers beibehalten. Die Lokalisierungsmiddleware liest das cookie bei aufeinanderfolgenden Anforderungen, um die Kultur des Benutzers festzulegen. 

Durch Verwendung eines cookie wird sichergestellt, wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann. Wenn die Lokalisierungsschemas auf dem URL-Pfad oder der Abfragezeichenfolge basieren, kann das Schema möglicherweise nicht mit WebSockets funktionieren, wodurch das Beibehalten der Kultur fehlschlägt. Daher wird die Verwendung eines cookie für die Lokalisierungskultur empfohlen.

Alle Vorgehensweisen können zum Zuweisen einer Kultur verwendet werden, wenn die Kultur in einem Lokalisierungscookie beibehalten wird. Wenn die App bereits über ein Lokalisierungsschema für serverseitiges ASP.NET Core verfügt, können Sie die vorhandene Lokalisierungsinfrastruktur der App weiterhin verwenden und das Lokalisierungskulturcookie innerhalb des Schemas der App festlegen.

Im folgenden Beispiel wird veranschaulicht, wie die aktuelle Kultur in einem cookie festgelegt werden kann, das von der Lokalisierungsmiddleware gelesen werden kann. Erstellen Sie in der `Pages/_Host.cshtml`-Datei direkt innerhalb des öffnenden `<body>`-Tags einen Razor-Ausdruck:

```cshtml
@using System.Globalization
@using Microsoft.AspNetCore.Localization

...

<body>
    @{
        this.HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }

    ...
</body>
```

Die Lokalisierung wird mit der folgenden Ereignissequenz von der App verarbeitet:

1. Der Browser sendet zunächst eine HTTP-Anforderung an die App.
1. Die Kultur wird von der Lokalisierungsmiddleware zugewiesen.
1. Der Razor-Ausdruck auf der `_Host`-Seite (`_Host.cshtml`) speichert die Kultur im Rahmen der Reaktion in einem cookie.
1. Der Browser stellt eine WebSocket-Verbindung her, um eine interaktive Blazor Server-Sitzung zu erstellen.
1. Die Lokalisierungsmiddleware liest das cookie und weist die Kultur zu.
1. Die Blazor Server-Sitzung beginnt mit der richtigen Kultur.

Verwenden Sie bei der Arbeit mit einer <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage> die <xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context>-Eigenschaft:

```razor
@{
    this.Context.Response.Cookies.Append(
        CookieRequestCultureProvider.DefaultCookieName,
        CookieRequestCultureProvider.MakeCookieValue(
            new RequestCulture(
                CultureInfo.CurrentCulture,
                CultureInfo.CurrentUICulture)));
}
```

#### <a name="provide-ui-to-choose-the-culture"></a>Bereitstellen einer Benutzeroberfläche zum Auswählen der Kultur

Zum Bereitstellen einer Benutzeroberfläche, um Benutzern das Auswählen einer Kultur zu ermöglichen, wird ein *Ansatz auf Grundlage von Umleitungen* empfohlen. Dieses Verfahren ähnelt dem, was in einer Web-App passiert, wenn ein Benutzer versucht, auf eine sichere Ressource zuzugreifen. Der Benutzer wird auf eine Anmeldeseite umgeleitet und dann zurück zur ursprünglichen Ressource. 

Die App behält die vom Benutzer ausgewählte Kultur mithilfe einer Umleitung an einen Controller bei. Der Controller legt die ausgewählte Kultur des Benutzers mit einem cookie fest und leitet den Benutzer wieder an den ursprünglichen URI weiter.

Erstellen Sie einen HTTP-Endpunkt auf dem Server zum Festlegen der ausgewählten Kultur eines Benutzers in einem cookie, und leiten Sie ihn dann wieder an den ursprünglichen URI weiter:

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> Verwenden Sie das Ergebnis der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A>-Aktion, um Angriffe durch offene Umleitungen zu verhindern. Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.

Befolgen Sie die folgenden Schritte, wenn die App nicht für die Verarbeitung von Controlleraktionen konfiguriert ist:

* Fügen Sie MVC-Dienste zur Dienstsammlung in `Startup.ConfigureServices` hinzu:

  ```csharp
  services.AddControllers();
  ```

* Fügen Sie das Controllerendpunktrouting in `Startup.Configure` hinzu:

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

Im folgenden Beispiel wird das Durchführen einer Umleitung veranschaulicht, wenn der Benutzer eine Kultur auswählt:

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/localization>
