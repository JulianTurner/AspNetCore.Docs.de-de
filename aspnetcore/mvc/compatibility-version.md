---
title: Kompatibilitätsversion für ASP.NET Core MVC
author: rick-anderson
description: Informationen zur Vorgehensweise der Startklasse in ASP.NET Core bei der Konfiguration von Diensten und der Anforderungspipeline einer App.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 9/25/2019
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
uid: mvc/compatibility-version
ms.openlocfilehash: b7d14aa0371fcc01d568cf1e7b9908c4a8d984df
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722831"
---
# <a name="compatibility-version-for-aspnet-core-mvc"></a><span data-ttu-id="23c29-103">Kompatibilitätsversion für ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="23c29-103">Compatibility version for ASP.NET Core MVC</span></span>

<span data-ttu-id="23c29-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="23c29-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="23c29-105">Die <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*>-Methode ist für ASP.NET Core 3.0-Apps keine Option.</span><span class="sxs-lookup"><span data-stu-id="23c29-105">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method is a no-op for ASP.NET Core 3.0 apps.</span></span> <span data-ttu-id="23c29-106">Das bedeutet, dass der Aufruf von `SetCompatibilityVersion` mit einem beliebigen Wert von <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> keine Auswirkungen auf die Anwendung hat.</span><span class="sxs-lookup"><span data-stu-id="23c29-106">That is, calling `SetCompatibilityVersion` with any value of <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion> has no impact on the application.</span></span>

* <span data-ttu-id="23c29-107">Die nächste Nebenversion von ASP.NET Core stellt möglicherweise einen neuen `CompatibilityVersion`-Wert bereit.</span><span class="sxs-lookup"><span data-stu-id="23c29-107">The next minor version of ASP.NET Core may provide a new `CompatibilityVersion` value.</span></span>
* <span data-ttu-id="23c29-108">`CompatibilityVersion`-Werte von `Version_2_0` bis `Version_2_2` (jeweils einschließlich) sind als `[Obsolete(...)]` gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="23c29-108">`CompatibilityVersion` values `Version_2_0` through `Version_2_2` are marked `[Obsolete(...)]`.</span></span>
* <span data-ttu-id="23c29-109">Weitere Informationen finden Sie unter [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387) (Breaking Changes bei APIs für Fälschungsschutz, CORS, Diagnosen, MVC und Routing).</span><span class="sxs-lookup"><span data-stu-id="23c29-109">See [Breaking API changes in Antiforgery, CORS, Diagnostics, Mvc, and Routing](https://github.com/aspnet/Announcements/issues/387).</span></span> <span data-ttu-id="23c29-110">Diese Liste enthält Breaking Changes für Kompatibilitätsoptionen.</span><span class="sxs-lookup"><span data-stu-id="23c29-110">This list includes breaking changes for compatibility switches.</span></span>

<span data-ttu-id="23c29-111">Um zu erfahren, wie `SetCompatibilityVersion` mit ASP.NET Core 2.x-Apps funktioniert, wählen Sie die [ASP.NET Core 2.2-Version dieses Artikels](?view=aspnetcore-2.2) aus.</span><span class="sxs-lookup"><span data-stu-id="23c29-111">To see how `SetCompatibilityVersion` works with ASP.NET Core 2.x apps, select the [ASP.NET Core 2.2 version of this article](?view=aspnetcore-2.2).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="23c29-112">Mit der Methode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> kann eine ASP.NET Core 2.x-App Änderungen im Verhalten aktivieren oder deaktivieren, die in ASP.NET Core MVC 2.1 oder 2.2 eingeführt wurden und Fehler verursachen können.</span><span class="sxs-lookup"><span data-stu-id="23c29-112">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an ASP.NET Core 2.x app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or 2.2.</span></span> <span data-ttu-id="23c29-113">Diese potentiell Fehler verursachenden Änderungen im Verhalten betreffen generell das Verhalten des MVC-Subsystems und die Art, wie **Ihr Code** von der Runtime aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="23c29-113">These potentially breaking behavior changes are generally in how the MVC subsystem behaves and how **your code** is called by the runtime.</span></span> <span data-ttu-id="23c29-114">Wenn Sie sich für die Änderungen entscheiden, erhalten Sie das aktuelle Verhalten und das langfristige Verhalten von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="23c29-114">By opting in, you get the latest behavior, and the long-term behavior of ASP.NET Core.</span></span>

<span data-ttu-id="23c29-115">Der folgende Code legt den Kompatibilitätsmodus auf ASP.NET Core 2.2 fest:</span><span class="sxs-lookup"><span data-stu-id="23c29-115">The following code sets the compatibility mode to ASP.NET Core 2.2:</span></span>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup.cs?name=snippet1)]

<span data-ttu-id="23c29-116">Es wird empfohlen, Ihre App mit der aktuellen Version zu testen (`CompatibilityVersion.Latest`).</span><span class="sxs-lookup"><span data-stu-id="23c29-116">We recommend you test your app using the latest version (`CompatibilityVersion.Latest`).</span></span> <span data-ttu-id="23c29-117">Wir erwarten, dass bei den meisten Apps mit der aktuellen Version keine Fehler verursachenden Verhaltensänderungen auftreten werden.</span><span class="sxs-lookup"><span data-stu-id="23c29-117">We anticipate that most apps won't have breaking behavior changes using the latest version.</span></span>

<span data-ttu-id="23c29-118">Apps, die `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` aufrufen, sind vor potenziell Fehler verursachenden Änderungen im Verhalten geschützt, die in den ASP.NET Core MVC-Versionen 2.1 bzw. 2.2 eingeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="23c29-118">Apps that call `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)` are protected from potentially breaking behavior changes introduced in the ASP.NET Core 2.1/2.2 MVC versions.</span></span> <span data-ttu-id="23c29-119">Dieser Schutz:</span><span class="sxs-lookup"><span data-stu-id="23c29-119">This protection:</span></span>

* <span data-ttu-id="23c29-120">Gilt nicht für alle Änderungen in 2.1 und höher. Das Ziel sind potentiell Fehler verursachende Änderungen im Verhalten der ASP.NET Core-Runtime im MVC-Subsystem.</span><span class="sxs-lookup"><span data-stu-id="23c29-120">Does not apply to all 2.1 and later changes, it's targeted to potentially breaking ASP.NET Core runtime behavior changes in the MVC subsystem.</span></span>
* <span data-ttu-id="23c29-121">Der Schutz gilt nicht für ASP.NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="23c29-121">Does not extend to ASP.NET Core 3.0.</span></span>

<span data-ttu-id="23c29-122">Standardmäßig gilt für ASP.NET Core 2.1- und 2.2-Apps, die **nicht**`SetCompatibilityVersion` aufrufen, die Kompatibilität mit Version 2.0.</span><span class="sxs-lookup"><span data-stu-id="23c29-122">The default compatibility for ASP.NET Core 2.1 and 2.2 apps that do **not** call `SetCompatibilityVersion` is 2.0 compatibility.</span></span> <span data-ttu-id="23c29-123">Das bedeutet, `SetCompatibilityVersion` nicht aufzurufen entspricht dem Aufrufen von `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span><span class="sxs-lookup"><span data-stu-id="23c29-123">That is, not calling `SetCompatibilityVersion` is the same as calling `SetCompatibilityVersion(CompatibilityVersion.Version_2_0)`.</span></span>

<span data-ttu-id="23c29-124">Der folgende Code legt den Kompatibilitätsmodus auf ASP.NET Core 2.2 fest, außer für die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="23c29-124">The following code sets the compatibility mode to ASP.NET Core 2.2, except for the following behaviors:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowCombiningAuthorizeFilters>
* <xref:Microsoft.AspNetCore.Mvc.MvcOptions.InputFormatterExceptionPolicy>

[!code-csharp[Main](compatibility-version/samples/2.x/CompatibilityVersionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="23c29-125">Bei Apps, bei denen Fehler verursachende Änderungen auftreten, können Sie die geeigneten Kompatibilitätsoptionen verwenden, um Folgendes zu erreichen:</span><span class="sxs-lookup"><span data-stu-id="23c29-125">For apps that encounter breaking behavior changes, using the appropriate compatibility switches:</span></span>

* <span data-ttu-id="23c29-126">Sie können das aktuelle Release verwenden und spezifische Fehler verursachende Änderungen im Verhalten vermeiden.</span><span class="sxs-lookup"><span data-stu-id="23c29-126">Allows you to use the latest release and opt out of specific breaking behavior changes.</span></span>
* <span data-ttu-id="23c29-127">Sie bekommen die Gelegenheit, Ihre App zu aktualisieren, damit Sie mit den neuesten Änderungen funktioniert.</span><span class="sxs-lookup"><span data-stu-id="23c29-127">Gives you time to update your app so it works with the latest changes.</span></span>

<span data-ttu-id="23c29-128">In der <xref:Microsoft.AspNetCore.Mvc.MvcOptions>-Dokumentation finden Sie eine gute Erklärung, was sich geändert hat und warum die Änderungen eine Verbesserung für die meisten Benutzer darstellen.</span><span class="sxs-lookup"><span data-stu-id="23c29-128">The <xref:Microsoft.AspNetCore.Mvc.MvcOptions> documentation has a good explanation of what changed and why the changes are an improvement for most users.</span></span>

<span data-ttu-id="23c29-129">Alte Verhaltensweisen, die von Kompatibilitätsoptionen unterstützt werden, wurden aus ASP.NET Core 3.0 entfernt.</span><span class="sxs-lookup"><span data-stu-id="23c29-129">With ASP.NET Core 3.0, old behaviors supported by compatibility switches have been removed.</span></span> <span data-ttu-id="23c29-130">Beinahe alle Benutzer werden von diesen positiven Änderungen profitieren.</span><span class="sxs-lookup"><span data-stu-id="23c29-130">We feel these are positive changes benefitting nearly all users.</span></span> <span data-ttu-id="23c29-131">Diese Änderungen in 2.1 und 2.2 bieten Vorteile für die meisten Apps, bei anderen bleibt genügend Zeit für ein Update.</span><span class="sxs-lookup"><span data-stu-id="23c29-131">By introducing these changes in 2.1 and 2.2, most apps can benefit, while others have time to update.</span></span>
::: moniker-end