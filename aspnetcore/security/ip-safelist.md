---
title: Client-IP-SafeList für ASP.net Core
author: damienbod
description: Erfahren Sie, wie Sie Middleware oder Aktionsfilter schreiben, um Remote-IP-Adressen anhand einer Liste genehmigter IP-Adressen zu überprüfen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/12/2020
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
uid: security/ip-safelist
ms.openlocfilehash: dfc134b97bb0976bc682a53d536cd27785550c7d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059662"
---
# <a name="client-ip-safelist-for-aspnet-core"></a><span data-ttu-id="9e3fd-103">Client-IP-SafeList für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9e3fd-103">Client IP safelist for ASP.NET Core</span></span>

<span data-ttu-id="9e3fd-104">Von [Damien Bowder](https://twitter.com/damien_bod) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="9e3fd-104">By [Damien Bowden](https://twitter.com/damien_bod) and [Tom Dykstra](https://github.com/tdykstra)</span></span>
 
<span data-ttu-id="9e3fd-105">In diesem Artikel werden drei Möglichkeiten beschrieben, wie Sie eine IP-Adresse Safelist (auch als Zulassungsliste bezeichnet) in einer ASP.net Core-App implementieren.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-105">This article shows three ways to implement an IP address safelist (also known as an allow list) in an ASP.NET Core app.</span></span> <span data-ttu-id="9e3fd-106">In einer begleitenden Beispiel-App werden alle drei Ansätze veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-106">An accompanying sample app demonstrates all three approaches.</span></span> <span data-ttu-id="9e3fd-107">Verwenden Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9e3fd-107">You can use:</span></span>

* <span data-ttu-id="9e3fd-108">Middleware zum Überprüfen der Remote-IP-Adresse für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-108">Middleware to check the remote IP address of every request.</span></span>
* <span data-ttu-id="9e3fd-109">MVC-Aktionsfilter zum Überprüfen der Remote-IP-Adresse von Anforderungen für bestimmte Controller oder Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-109">MVC action filters to check the remote IP address of requests for specific controllers or action methods.</span></span>
* <span data-ttu-id="9e3fd-110">Razor Seiten Filter zum Überprüfen der Remote-IP-Adresse von Razor Seiten Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-110">Razor Pages filters to check the remote IP address of requests for Razor pages.</span></span>

<span data-ttu-id="9e3fd-111">In jedem Fall wird eine Zeichenfolge mit genehmigten Client-IP-Adressen in einer APP-Einstellung gespeichert.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-111">In each case, a string containing approved client IP addresses is stored in an app setting.</span></span> <span data-ttu-id="9e3fd-112">Die Middleware oder der Filter:</span><span class="sxs-lookup"><span data-stu-id="9e3fd-112">The middleware or filter:</span></span>

* <span data-ttu-id="9e3fd-113">Analysiert die Zeichenfolge in ein Array.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-113">Parses the string into an array.</span></span> 
* <span data-ttu-id="9e3fd-114">Überprüft, ob die Remote-IP-Adresse im Array vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-114">Checks if the remote IP address exists in the array.</span></span>

<span data-ttu-id="9e3fd-115">Der Zugriff ist zulässig, wenn das Array die IP-Adresse enthält.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-115">Access is allowed if the array contains the IP address.</span></span> <span data-ttu-id="9e3fd-116">Andernfalls wird ein HTTP 403-Statuscode "verboten" zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-116">Otherwise, an HTTP 403 Forbidden status code is returned.</span></span>

<span data-ttu-id="9e3fd-117">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9e3fd-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/ip-safelist/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ip-address-safelist"></a><span data-ttu-id="9e3fd-118">IP-Adresse SafeList</span><span class="sxs-lookup"><span data-stu-id="9e3fd-118">IP address safelist</span></span>

<span data-ttu-id="9e3fd-119">In der Beispiel-App lautet die IP-Adresse SafeList:</span><span class="sxs-lookup"><span data-stu-id="9e3fd-119">In the sample app, the IP address safelist is:</span></span>

* <span data-ttu-id="9e3fd-120">Wird von der- `AdminSafeList` Eigenschaft in der *appsettings.json* Datei definiert.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-120">Defined by the `AdminSafeList` property in the *appsettings.json* file.</span></span>
* <span data-ttu-id="9e3fd-121">Eine durch Semikolons getrennte Zeichenfolge, die sowohl [IPv4 (Internet Protocol Version 4)](https://wikipedia.org/wiki/IPv4) -als auch IPv6-Adressen [(Internet Protocol Version 6)](https://wikipedia.org/wiki/IPv6) enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-121">A semicolon-delimited string that may contain both [Internet Protocol version 4 (IPv4)](https://wikipedia.org/wiki/IPv4) and [Internet Protocol version 6 (IPv6)](https://wikipedia.org/wiki/IPv6) addresses.</span></span>

[!code-json[](ip-safelist/samples/3.x/ClientIpAspNetCore/appsettings.json?range=1-3&highlight=2)]

<span data-ttu-id="9e3fd-122">Im vorherigen Beispiel sind die IPv4-Adressen von `127.0.0.1` und `192.168.1.5` und die IPv6-Loopback Adresse `::1` (komprimiertes Format für `0:0:0:0:0:0:0:1` ) zulässig.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-122">In the preceding example, the IPv4 addresses of `127.0.0.1` and `192.168.1.5` and the IPv6 loopback address of `::1` (compressed format for `0:0:0:0:0:0:0:1`) are allowed.</span></span>

## <a name="middleware"></a><span data-ttu-id="9e3fd-123">Middleware</span><span class="sxs-lookup"><span data-stu-id="9e3fd-123">Middleware</span></span>

<span data-ttu-id="9e3fd-124">Die `Startup.Configure` -Methode fügt der `AdminSafeListMiddleware` Anforderungs Pipeline der APP den Typ der benutzerdefinierten Middleware hinzu.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-124">The `Startup.Configure` method adds the custom `AdminSafeListMiddleware` middleware type to the app's request pipeline.</span></span> <span data-ttu-id="9e3fd-125">Die SafeList wird mit dem .net Core-Konfigurations Anbieter abgerufen und als Konstruktorparameter übergeben.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-125">The safelist is retrieved with the .NET Core configuration provider and is passed as a constructor parameter.</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureAddMiddleware)]

<span data-ttu-id="9e3fd-126">Die Middleware analysiert die Zeichenfolge in ein Array und sucht im Array nach der Remote-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-126">The middleware parses the string into an array and searches for the remote IP address in the array.</span></span> <span data-ttu-id="9e3fd-127">Wenn die Remote-IP-Adresse nicht gefunden wird, gibt die Middleware HTTP 403 unzulässig zurück.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-127">If the remote IP address isn't found, the middleware returns HTTP 403 Forbidden.</span></span> <span data-ttu-id="9e3fd-128">Dieser Überprüfungs Vorgang wird für HTTP GET-Anforderungen umgangen.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-128">This validation process is bypassed for HTTP GET requests.</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Middlewares/AdminSafeListMiddleware.cs?name=snippet_ClassOnly)]

## <a name="action-filter"></a><span data-ttu-id="9e3fd-129">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="9e3fd-129">Action filter</span></span>

<span data-ttu-id="9e3fd-130">Wenn Sie die SafeList-gesteuerte Zugriffs Steuerung für bestimmte MVC-Controller oder Aktionsmethoden verwenden möchten, verwenden Sie einen Aktionsfilter.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-130">If you want safelist-driven access control for specific MVC controllers or action methods, use an action filter.</span></span> <span data-ttu-id="9e3fd-131">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9e3fd-131">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckActionFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="9e3fd-132">`Startup.ConfigureServices`Fügen Sie in den Aktionsfilter der MVC-Filter Auflistung hinzu.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-132">In `Startup.ConfigureServices`, add the action filter to the MVC filters collection.</span></span> <span data-ttu-id="9e3fd-133">Im folgenden Beispiel `ClientIpCheckActionFilter` wird ein Aktionsfilter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-133">In the following example, a `ClientIpCheckActionFilter` action filter is added.</span></span> <span data-ttu-id="9e3fd-134">Eine SafeList und eine Konsolen Protokollierungs Instanz werden als Konstruktorparameter übergeben.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-134">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesActionFilter)]

::: moniker-end

<span data-ttu-id="9e3fd-135">Der Aktionsfilter kann dann mit dem [[Service Filter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) -Attribut auf einen Controller oder eine Aktionsmethode angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="9e3fd-135">The action filter can then be applied to a controller or action method with the [[ServiceFilter]](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute) attribute:</span></span>

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Controllers/ValuesController.cs?name=snippet_ActionFilter&highlight=1)]

<span data-ttu-id="9e3fd-136">In der Beispiel-APP wird der Aktionsfilter auf die Aktionsmethode des Controllers angewendet `Get` .</span><span class="sxs-lookup"><span data-stu-id="9e3fd-136">In the sample app, the action filter is applied to the controller's `Get` action method.</span></span> <span data-ttu-id="9e3fd-137">Wenn Sie die APP testen, indem Sie Folgendes senden:</span><span class="sxs-lookup"><span data-stu-id="9e3fd-137">When you test the app by sending:</span></span>

* <span data-ttu-id="9e3fd-138">Eine HTTP GET-Anforderung, das `[ServiceFilter]` -Attribut überprüft die Client-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-138">An HTTP GET request, the `[ServiceFilter]` attribute validates the client IP address.</span></span> <span data-ttu-id="9e3fd-139">Wenn der Zugriff auf die `Get` Aktionsmethode zulässig ist, wird eine Variation der folgenden Konsolenausgabe durch den Aktionsfilter und die Aktionsmethode erzeugt:</span><span class="sxs-lookup"><span data-stu-id="9e3fd-139">If access is allowed to the `Get` action method, a variation of the following console output is produced by the action filter and action method:</span></span>

    ```
    dbug: ClientIpSafelistComponents.Filters.ClientIpCheckActionFilter[0]
          Remote IpAddress: ::1
    dbug: ClientIpAspNetCore.Controllers.ValuesController[0]
          successful HTTP GET    
    ```

* <span data-ttu-id="9e3fd-140">Ein anderes HTTP-Anforderungs Verb als Get, die `AdminSafeListMiddleware` Middleware überprüft die Client-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-140">An HTTP request verb other than GET, the `AdminSafeListMiddleware` middleware validates the client IP address.</span></span>

## <a name="no-locrazor-pages-filter"></a><span data-ttu-id="9e3fd-141">Razor Seiten Filter</span><span class="sxs-lookup"><span data-stu-id="9e3fd-141">Razor Pages filter</span></span>

<span data-ttu-id="9e3fd-142">Wenn Sie die SafeList-gesteuerte Zugriffs Steuerung für eine Razor pages-App verwenden möchten, verwenden Sie einen Razor Seiten Filter.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-142">If you want safelist-driven access control for a Razor Pages app, use a Razor Pages filter.</span></span> <span data-ttu-id="9e3fd-143">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9e3fd-143">For example:</span></span>

[!code-csharp[](ip-safelist/samples/Shared/ClientIpSafelistComponents/Filters/ClientIpCheckPageFilter.cs?name=snippet_ClassOnly)]

<span data-ttu-id="9e3fd-144">`Startup.ConfigureServices`Aktivieren Sie in den Razor Seiten Filter, indem Sie ihn der MVC-Filter Auflistung hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-144">In `Startup.ConfigureServices`, enable the Razor Pages filter by adding it to the MVC filters collection.</span></span> <span data-ttu-id="9e3fd-145">Im folgenden Beispiel `ClientIpCheckPageFilter` Razor wird ein Seiten Filter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-145">In the following example, a `ClientIpCheckPageFilter` Razor Pages filter is added.</span></span> <span data-ttu-id="9e3fd-146">Eine SafeList und eine Konsolen Protokollierungs Instanz werden als Konstruktorparameter übergeben.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-146">A safelist and a console logger instance are passed as constructor parameters.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](ip-safelist/samples/3.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](ip-safelist/samples/2.x/ClientIpAspNetCore/Startup.cs?name=snippet_ConfigureServicesPageFilter)]

::: moniker-end

<span data-ttu-id="9e3fd-147">Wenn die *Index* Seite der Beispiel-APP Razor angefordert wird, Razor überprüft der Seiten Filter die Client-IP-Adresse.</span><span class="sxs-lookup"><span data-stu-id="9e3fd-147">When the sample app's *Index* Razor page is requested, the Razor Pages filter validates the client IP address.</span></span> <span data-ttu-id="9e3fd-148">Der Filter erzeugt eine Variation der folgenden Konsolenausgabe:</span><span class="sxs-lookup"><span data-stu-id="9e3fd-148">The filter produces a variation of the following console output:</span></span>

```
dbug: ClientIpSafelistComponents.Filters.ClientIpCheckPageFilter[0]
      Remote IpAddress: ::1
```

## <a name="additional-resources"></a><span data-ttu-id="9e3fd-149">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9e3fd-149">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="9e3fd-150">Aktionsfilter</span><span class="sxs-lookup"><span data-stu-id="9e3fd-150">Action filters</span></span>](xref:mvc/controllers/filters#action-filters)
* <xref:razor-pages/filter>
