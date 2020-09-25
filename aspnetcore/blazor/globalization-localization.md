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
ms.openlocfilehash: 2b8820acba564bdfb85f8338ed5482573960fbb4
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080276"
---
# <a name="aspnet-core-no-locblazor-globalization-and-localization"></a><span data-ttu-id="21ca0-103">Globalisierung und Lokalisierung in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="21ca0-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="21ca0-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="21ca0-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="21ca0-105">Razor-Komponenten können Benutzern mit verschiedenen Kulturen und Sprachen zur Verfügung gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="21ca0-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="21ca0-106">Die folgenden Szenarios zur Globalisierung und Lokalisierung von .NET sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="21ca0-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="21ca0-107">.NET-Ressourcensystem</span><span class="sxs-lookup"><span data-stu-id="21ca0-107">.NET's resources system</span></span>
* <span data-ttu-id="21ca0-108">Kulturspezifische Zahlen- und Datumsformatierung</span><span class="sxs-lookup"><span data-stu-id="21ca0-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="21ca0-109">Derzeit werden Lokalisierungsszenarios für ASP.NET Core eingeschränkt unterstützt:</span><span class="sxs-lookup"><span data-stu-id="21ca0-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="21ca0-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> und <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> werden in Blazor-Apps *unterstützt*.</span><span class="sxs-lookup"><span data-stu-id="21ca0-110"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> *are supported* in Blazor apps.</span></span>
* <span data-ttu-id="21ca0-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer> und die Lokalisierung von Datenanmerkungen sind ASP.NET Core MVC-Szenarios und **werden nicht in Blazor-Apps unterstützt**.</span><span class="sxs-lookup"><span data-stu-id="21ca0-111"><xref:Microsoft.AspNetCore.Mvc.Localization.IHtmlLocalizer>, <xref:Microsoft.AspNetCore.Mvc.Localization.IViewLocalizer>, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="21ca0-112">Weitere Informationen finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="21ca0-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="21ca0-113">Globalisierung</span><span class="sxs-lookup"><span data-stu-id="21ca0-113">Globalization</span></span>

<span data-ttu-id="21ca0-114">Die [`@bind`](xref:mvc/views/razor#bind)-Funktionalität von Blazor führt die Formatierung durch und analysiert Werte, um die Anzeige an die Kultur des jeweiligen Benutzers anzupassen.</span><span class="sxs-lookup"><span data-stu-id="21ca0-114">Blazor's [`@bind`](xref:mvc/views/razor#bind) functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="21ca0-115">Der Zugriff auf die aktuelle Kultur kann über die <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>-Eigenschaft erfolgen.</span><span class="sxs-lookup"><span data-stu-id="21ca0-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="21ca0-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> wird für die folgenden Feldtypen verwendet (`<input type="{TYPE}" />`):</span><span class="sxs-lookup"><span data-stu-id="21ca0-116"><xref:System.Globalization.CultureInfo.InvariantCulture?displayProperty=nameWithType> is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="21ca0-117">Diese Feldtypen:</span><span class="sxs-lookup"><span data-stu-id="21ca0-117">The preceding field types:</span></span>

* <span data-ttu-id="21ca0-118">werden basierend auf ihren entsprechenden browserbasierten Formatierungsregeln angezeigt.</span><span class="sxs-lookup"><span data-stu-id="21ca0-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="21ca0-119">können keinen Freiformtext enthalten.</span><span class="sxs-lookup"><span data-stu-id="21ca0-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="21ca0-120">bieten Benutzerinteraktionsmerkmale basierend auf der Implementierung des Browsers.</span><span class="sxs-lookup"><span data-stu-id="21ca0-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="21ca0-121">Die folgenden Feldtypen verfügen über spezifische Formatierungsanforderungen und werden derzeit nicht von Blazor unterstützt, weil sie von keinem der gängigen Browser unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="21ca0-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="21ca0-122">[`@bind`](xref:mvc/views/razor#bind) unterstützt den Parameter `@bind:culture`, um eine Klasse <xref:System.Globalization.CultureInfo?displayProperty=fullName> für das Analysieren und Formatieren von Werten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="21ca0-122">[`@bind`](xref:mvc/views/razor#bind) supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="21ca0-123">Vom Festlegen einer Kultur wird abgeraten, wenn die Feldtypen `date` und `number` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="21ca0-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="21ca0-124">`date` und `number` verfügen über integrierte Blazor-Unterstützung, die die erforderliche Kultur bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="21ca0-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="21ca0-125">Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="21ca0-125">Localization</span></span>

### Blazor WebAssembly

<span data-ttu-id="21ca0-126">Blazor WebAssembly-Apps legen die Kultur anhand der [Spracheinstellungen](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages) des Benutzers fest.</span><span class="sxs-lookup"><span data-stu-id="21ca0-126">Blazor WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="21ca0-127">Legen Sie in `Program.Main` <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> und <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> fest, um die Kultur explizit zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="21ca0-127">To explicitly configure the culture, set <xref:System.Globalization.CultureInfo.DefaultThreadCurrentCulture?displayProperty=nameWithType> and <xref:System.Globalization.CultureInfo.DefaultThreadCurrentUICulture?displayProperty=nameWithType> in `Program.Main`.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="21ca0-128">Blazor WebAssembly enthält standardmäßig Globalisierungsressourcen, die zum Anzeigen von Werten wie Datums- und Währungsangaben in der Kultur des Benutzers erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="21ca0-128">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="21ca0-129">Wenn für die App keine Lokalisierung erforderlich ist, können Sie die App so konfigurieren, dass die invariante Kultur unterstützt wird, die auf der `en-US`-Kultur basiert:</span><span class="sxs-lookup"><span data-stu-id="21ca0-129">If the app doesn't require localization, you may configure the app to support the invariant culture, which is based on the `en-US` culture:</span></span>

```xml
<PropertyGroup>
  <InvariantGlobalization>true</InvariantGlobalization>
</PropertyGroup>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="21ca0-130">Mit der IL-Linkerkonfiguration (Intermediate Language, Zwischensprache) für Blazor WebAssembly-Apps werden mit Ausnahme von explizit angeforderten Gebietsschemas alle Internationalisierungsinformationen standardmäßig entfernt.</span><span class="sxs-lookup"><span data-stu-id="21ca0-130">By default, the Intermediate Language (IL) Linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="21ca0-131">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="21ca0-131">For more information, see <xref:blazor/host-and-deploy/configure-linker#configure-the-linker-for-internationalization>.</span></span>

::: moniker-end

<span data-ttu-id="21ca0-132">Auch wenn für die meisten Benutzer die standardmäßig von Blazor ausgewählte Kultur möglicherweise ausreichend ist, ziehen Sie in Betracht, Benutzern die Möglichkeit zu geben, ihr bevorzugtes Gebietsschema anzugeben.</span><span class="sxs-lookup"><span data-stu-id="21ca0-132">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="21ca0-133">Eine Blazor WebAssembly-Beispiel-App mit Kulturauswahl finden Sie in [`LocSample`](https://github.com/pranavkm/LocSample), der Beispiel-App für die Lokalisierung.</span><span class="sxs-lookup"><span data-stu-id="21ca0-133">For a Blazor WebAssembly sample app with a culture picker, see the [`LocSample`](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### Blazor Server

<span data-ttu-id="21ca0-134">Blazor Server-Apps werden mit [Lokalisierungsmiddleware](xref:fundamentals/localization#localization-middleware) lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="21ca0-134">Blazor Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="21ca0-135">Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der App anfordern.</span><span class="sxs-lookup"><span data-stu-id="21ca0-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="21ca0-136">Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="21ca0-136">The culture can be set using one of the following approaches:</span></span>

* <span data-ttu-id="21ca0-137">[Cookies](#cookies)</span><span class="sxs-lookup"><span data-stu-id="21ca0-137">[Cookies](#cookies)</span></span>
* [<span data-ttu-id="21ca0-138">Bereitstellen einer Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="21ca0-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="21ca0-139">Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="21ca0-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="no-loccookies"></a><span data-ttu-id="21ca0-140">Cookie</span><span class="sxs-lookup"><span data-stu-id="21ca0-140">Cookies</span></span>

<span data-ttu-id="21ca0-141">Ein cookie für die Lokalisierungskultur kann die Kultur des Benutzers beibehalten.</span><span class="sxs-lookup"><span data-stu-id="21ca0-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="21ca0-142">Die Lokalisierungsmiddleware liest das cookie bei aufeinanderfolgenden Anforderungen, um die Kultur des Benutzers festzulegen.</span><span class="sxs-lookup"><span data-stu-id="21ca0-142">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="21ca0-143">Durch Verwendung eines cookie wird sichergestellt, wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann.</span><span class="sxs-lookup"><span data-stu-id="21ca0-143">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="21ca0-144">Wenn die Lokalisierungsschemas auf dem URL-Pfad oder der Abfragezeichenfolge basieren, kann das Schema möglicherweise nicht mit WebSockets funktionieren, wodurch das Beibehalten der Kultur fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="21ca0-144">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="21ca0-145">Daher wird die Verwendung eines cookie für die Lokalisierungskultur empfohlen.</span><span class="sxs-lookup"><span data-stu-id="21ca0-145">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="21ca0-146">Alle Vorgehensweisen können zum Zuweisen einer Kultur verwendet werden, wenn die Kultur in einem Lokalisierungscookie beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="21ca0-146">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="21ca0-147">Wenn die App bereits über ein Lokalisierungsschema für serverseitiges ASP.NET Core verfügt, können Sie die vorhandene Lokalisierungsinfrastruktur der App weiterhin verwenden und das Lokalisierungskulturcookie innerhalb des Schemas der App festlegen.</span><span class="sxs-lookup"><span data-stu-id="21ca0-147">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="21ca0-148">Im folgenden Beispiel wird veranschaulicht, wie die aktuelle Kultur in einem cookie festgelegt werden kann, das von der Lokalisierungsmiddleware gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="21ca0-148">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="21ca0-149">Erstellen Sie in der `Pages/_Host.cshtml`-Datei direkt innerhalb des öffnenden `<body>`-Tags einen Razor-Ausdruck:</span><span class="sxs-lookup"><span data-stu-id="21ca0-149">Create a Razor expression in the `Pages/_Host.cshtml` file immediately inside the opening `<body>` tag:</span></span>

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

<span data-ttu-id="21ca0-150">Die Lokalisierung wird mit der folgenden Ereignissequenz von der App verarbeitet:</span><span class="sxs-lookup"><span data-stu-id="21ca0-150">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="21ca0-151">Der Browser sendet zunächst eine HTTP-Anforderung an die App.</span><span class="sxs-lookup"><span data-stu-id="21ca0-151">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="21ca0-152">Die Kultur wird von der Lokalisierungsmiddleware zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="21ca0-152">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="21ca0-153">Der Razor-Ausdruck auf der `_Host`-Seite (`_Host.cshtml`) speichert die Kultur im Rahmen der Reaktion in einem cookie.</span><span class="sxs-lookup"><span data-stu-id="21ca0-153">The Razor expression in the `_Host` page (`_Host.cshtml`) persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="21ca0-154">Der Browser stellt eine WebSocket-Verbindung her, um eine interaktive Blazor Server-Sitzung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="21ca0-154">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="21ca0-155">Die Lokalisierungsmiddleware liest das cookie und weist die Kultur zu.</span><span class="sxs-lookup"><span data-stu-id="21ca0-155">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="21ca0-156">Die Blazor Server-Sitzung beginnt mit der richtigen Kultur.</span><span class="sxs-lookup"><span data-stu-id="21ca0-156">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="21ca0-157">Bereitstellen einer Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="21ca0-157">Provide UI to choose the culture</span></span>

<span data-ttu-id="21ca0-158">Zum Bereitstellen einer Benutzeroberfläche, um Benutzern das Auswählen einer Kultur zu ermöglichen, wird ein *Ansatz auf Grundlage von Umleitungen* empfohlen.</span><span class="sxs-lookup"><span data-stu-id="21ca0-158">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="21ca0-159">Dieses Verfahren ähnelt dem, was in einer Web-App passiert, wenn ein Benutzer versucht, auf eine sichere Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="21ca0-159">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="21ca0-160">Der Benutzer wird auf eine Anmeldeseite umgeleitet und dann zurück zur ursprünglichen Ressource.</span><span class="sxs-lookup"><span data-stu-id="21ca0-160">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="21ca0-161">Die App behält die vom Benutzer ausgewählte Kultur mithilfe einer Umleitung an einen Controller bei.</span><span class="sxs-lookup"><span data-stu-id="21ca0-161">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="21ca0-162">Der Controller legt die ausgewählte Kultur des Benutzers mit einem cookie fest und leitet den Benutzer wieder an den ursprünglichen URI weiter.</span><span class="sxs-lookup"><span data-stu-id="21ca0-162">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="21ca0-163">Erstellen Sie einen HTTP-Endpunkt auf dem Server zum Festlegen der ausgewählten Kultur eines Benutzers in einem cookie, und leiten Sie ihn dann wieder an den ursprünglichen URI weiter:</span><span class="sxs-lookup"><span data-stu-id="21ca0-163">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

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
> <span data-ttu-id="21ca0-164">Verwenden Sie das Ergebnis der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A>-Aktion, um Angriffe durch offene Umleitungen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="21ca0-164">Use the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.LocalRedirect%2A> action result to prevent open redirect attacks.</span></span> <span data-ttu-id="21ca0-165">Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="21ca0-165">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="21ca0-166">Befolgen Sie die folgenden Schritte, wenn die App nicht für die Verarbeitung von Controlleraktionen konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="21ca0-166">If the app isn't configured to process controller actions:</span></span>

* <span data-ttu-id="21ca0-167">Fügen Sie MVC-Dienste zur Dienstsammlung in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="21ca0-167">Add MVC services to the service collection in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddControllers();
  ```

* <span data-ttu-id="21ca0-168">Fügen Sie das Controllerendpunktrouting in `Startup.Configure` hinzu:</span><span class="sxs-lookup"><span data-stu-id="21ca0-168">Add controller endpoint routing in `Startup.Configure`:</span></span>

  ```csharp
  app.UseEndpoints(endpoints =>
  {
      endpoints.MapControllers();
      endpoints.MapBlazorHub();
      endpoints.MapFallbackToPage("/_Host");
  });
  ```

<span data-ttu-id="21ca0-169">Im folgenden Beispiel wird das Durchführen einer Umleitung veranschaulicht, wenn der Benutzer eine Kultur auswählt:</span><span class="sxs-lookup"><span data-stu-id="21ca0-169">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="21ca0-170">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="21ca0-170">Additional resources</span></span>

* <xref:fundamentals/localization>
