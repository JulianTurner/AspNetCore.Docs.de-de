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
# <a name="localization-extensibility"></a><span data-ttu-id="a8735-103">Lokalisierungserweiterbarkeit</span><span class="sxs-lookup"><span data-stu-id="a8735-103">Localization Extensibility</span></span>

<span data-ttu-id="a8735-104">Von [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="a8735-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="a8735-105">Dieser Artikel:</span><span class="sxs-lookup"><span data-stu-id="a8735-105">This article:</span></span>

* <span data-ttu-id="a8735-106">Listet die Erweiterbarkeitspunkte für die Lokalisierungs-APIs auf.</span><span class="sxs-lookup"><span data-stu-id="a8735-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="a8735-107">Enthält Anweisungen zum Erweitern der ASP.NET Core-App-Lokalisierung.</span><span class="sxs-lookup"><span data-stu-id="a8735-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="a8735-108">Erweiterbare Punkte in Lokalisierungs-APIs</span><span class="sxs-lookup"><span data-stu-id="a8735-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="a8735-109">ASP.NET Core-Lokalisierungs-APIs sind für Erweiterbarkeit konzipiert.</span><span class="sxs-lookup"><span data-stu-id="a8735-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="a8735-110">Erweiterbarkeit ermöglicht es Entwicklern, die Lokalisierung entsprechend ihren Anforderungen anzupassen.</span><span class="sxs-lookup"><span data-stu-id="a8735-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="a8735-111">Beispielsweise verfügt [OrchardCore](https://github.com/orchardCMS/OrchardCore/) über einen `POStringLocalizer`.</span><span class="sxs-lookup"><span data-stu-id="a8735-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="a8735-112">`POStringLocalizer` beschreibt ausführlich die Verwendung der [Lokalisierung portabler Objekte](xref:fundamentals/portable-object-localization), um `PO`-Dateien zum Speichern von Lokalisierungsressourcen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="a8735-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="a8735-113">In diesem Artikel werden die beiden wichtigsten Erweiterbarkeitspunkte aufgelistet, die von Lokalisierungs-APIs bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="a8735-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="a8735-114">Lokalisierungskulturanbieter</span><span class="sxs-lookup"><span data-stu-id="a8735-114">Localization Culture Providers</span></span>

<span data-ttu-id="a8735-115">ASP.NET Core-Lokalisierungs-APIs verfügen über vier Standardanbieter, die die aktuelle Kultur einer aktuell ausgeführten Anforderung bestimmen können:</span><span class="sxs-lookup"><span data-stu-id="a8735-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="a8735-116">Die oben genannten Anbieter werden ausführlich in der Dokumentation zur [Lokalisierungsmiddleware](xref:fundamentals/localization) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="a8735-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="a8735-117">Wenn die Standardanbieter Ihre Anforderungen nicht erfüllen, erstellen Sie einen benutzerdefinierten Anbieter, indem Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="a8735-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="a8735-118">Verwenden von CustomRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="a8735-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="a8735-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> stellt einen benutzerdefinierten <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> bereit, der einen einfachen Delegaten verwendet, um die aktuelle Lokalisierungskultur zu ermitteln:</span><span class="sxs-lookup"><span data-stu-id="a8735-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

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

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="a8735-120">Verwenden einer neuen Implementierung von RequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="a8735-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="a8735-121">Eine neue Implementierung von <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> kann erstellt werden, die die Informationen zur Anforderungskultur aus einer benutzerdefinierten Quelle bestimmt.</span><span class="sxs-lookup"><span data-stu-id="a8735-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="a8735-122">Beispielsweise kann es sich bei der benutzerdefinierten Quelle um eine Konfigurationsdatei oder Datenbank handeln.</span><span class="sxs-lookup"><span data-stu-id="a8735-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="a8735-123">Das folgende Beispiel zeigt `AppSettingsRequestCultureProvider`, wodurch <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> erweitert wird, um die Anforderungskulturinformationen aus *appsettings.json* zu ermitteln:</span><span class="sxs-lookup"><span data-stu-id="a8735-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

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

## <a name="localization-resources"></a><span data-ttu-id="a8735-124">Lokalisierungsressourcen</span><span class="sxs-lookup"><span data-stu-id="a8735-124">Localization resources</span></span>

<span data-ttu-id="a8735-125">Die ASP.NET Core-Lokalisierung stellt <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> bereit.</span><span class="sxs-lookup"><span data-stu-id="a8735-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="a8735-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> ist eine Implementierung von <xref:Microsoft.Extensions.Localization.IStringLocalizer>, die `resx` verwendet, um Lokalisierungsressourcen zu speichern.</span><span class="sxs-lookup"><span data-stu-id="a8735-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="a8735-127">Sie sind nicht auf die Verwendung von `resx`-Dateien beschränkt.</span><span class="sxs-lookup"><span data-stu-id="a8735-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="a8735-128">Durch Implementieren von `IStringLocalizer` kann jede beliebige Datenquelle verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a8735-128">By implementing `IStringLocalizer`, any data source can be used.</span></span>

<span data-ttu-id="a8735-129">Die folgenden Beispielprojekte implementieren <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span><span class="sxs-lookup"><span data-stu-id="a8735-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="a8735-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="a8735-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="a8735-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="a8735-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="a8735-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="a8735-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)
