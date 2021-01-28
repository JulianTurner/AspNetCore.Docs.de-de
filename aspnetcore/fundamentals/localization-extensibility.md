---
title: Lokalisierungserweiterbarkeit
author: hishamco
description: Erfahren Sie, wie Sie die Lokalisierungs-APIs in ASP.NET Core-Apps erweitern.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
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
uid: fundamentals/localization-extensibility
ms.openlocfilehash: c41ebc0506a565666963d9b4c70ed73df389c826
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751661"
---
# <a name="localization-extensibility"></a>Lokalisierungserweiterbarkeit

Von [Hisham Bin Ateya](https://github.com/hishamco)

Dieser Artikel:

* Listet die Erweiterbarkeitspunkte für die Lokalisierungs-APIs auf.
* Enthält Anweisungen zum Erweitern der ASP.NET Core-App-Lokalisierung.

## <a name="extensible-points-in-localization-apis"></a>Erweiterbare Punkte in Lokalisierungs-APIs

ASP.NET Core-Lokalisierungs-APIs sind für Erweiterbarkeit konzipiert. Erweiterbarkeit ermöglicht es Entwicklern, die Lokalisierung entsprechend ihren Anforderungen anzupassen. Beispielsweise verfügt [OrchardCore](https://github.com/orchardCMS/OrchardCore/) über einen `POStringLocalizer`. `POStringLocalizer` beschreibt ausführlich die Verwendung der [Lokalisierung portabler Objekte](xref:fundamentals/portable-object-localization), um `PO`-Dateien zum Speichern von Lokalisierungsressourcen zu verwenden.

In diesem Artikel werden die beiden wichtigsten Erweiterbarkeitspunkte aufgelistet, die von Lokalisierungs-APIs bereitgestellt werden: 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>Lokalisierungskulturanbieter

ASP.NET Core-Lokalisierungs-APIs verfügen über vier Standardanbieter, die die aktuelle Kultur einer aktuell ausgeführten Anforderung bestimmen können:

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

Die oben genannten Anbieter werden ausführlich in der Dokumentation zur [Lokalisierungsmiddleware](xref:fundamentals/localization) beschrieben. Wenn die Standardanbieter Ihre Anforderungen nicht erfüllen, erstellen Sie einen benutzerdefinierten Anbieter, indem Sie einen der folgenden Ansätze verwenden:

### <a name="use-customrequestcultureprovider"></a>Verwenden von CustomRequestCultureProvider

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> stellt einen benutzerdefinierten <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> bereit, der einen einfachen Delegaten verwendet, um die aktuelle Lokalisierungskultur zu ermitteln:

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>Verwenden einer neuen Implementierung von RequestCultureProvider

Eine neue Implementierung von <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> kann erstellt werden, die die Informationen zur Anforderungskultur aus einer benutzerdefinierten Quelle bestimmt. Beispielsweise kann es sich bei der benutzerdefinierten Quelle um eine Konfigurationsdatei oder Datenbank handeln.

Das folgende Beispiel zeigt `AppSettingsRequestCultureProvider`, wodurch <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> erweitert wird, um die Anforderungskulturinformationen aus *appsettings.json* zu ermitteln:

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a>Lokalisierungsressourcen

Die ASP.NET Core-Lokalisierung stellt <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> bereit. <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> ist eine Implementierung von <xref:Microsoft.Extensions.Localization.IStringLocalizer>, die `resx` verwendet, um Lokalisierungsressourcen zu speichern.

Sie sind nicht auf die Verwendung von `resx`-Dateien beschränkt. Durch Implementieren von `IStringLocalizer` kann jede beliebige Datenquelle verwendet werden.

Die folgenden Beispielprojekte implementieren <xref:Microsoft.Extensions.Localization.IStringLocalizer>: 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
