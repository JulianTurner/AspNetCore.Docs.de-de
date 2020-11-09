---
title: Wieder verwenden von Objekten mit Objectpool in ASP.net Core
author: rick-anderson
description: Tipps zum Steigern der Leistung in ASP.net Core-apps mit Objectpool.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/ObjectPool
ms.openlocfilehash: 3bbfde6f68a238131149e67ce72f0ddc68a9ea0f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056906"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="cd438-103">Wieder verwenden von Objekten mit Objectpool in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="cd438-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="cd438-104">Von [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak)und [Günther Foidl](https://github.com/gfoidl)</span><span class="sxs-lookup"><span data-stu-id="cd438-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Günther Foidl](https://github.com/gfoidl)</span></span>

<span data-ttu-id="cd438-105"><xref:Microsoft.Extensions.ObjectPool> ist Teil der ASP.net Core-Infrastruktur, die unterstützt, dass eine Gruppe von Objekten für die Wiederverwendung im Speicher beibehalten wird, anstatt die Garbage Collection der Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="cd438-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="cd438-106">Möglicherweise möchten Sie den Objekt Pool verwenden, wenn die verwalteten Objekte wie folgt lauten:</span><span class="sxs-lookup"><span data-stu-id="cd438-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="cd438-107">Das Zuordnen/initialisieren ist aufwendig.</span><span class="sxs-lookup"><span data-stu-id="cd438-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="cd438-108">Stellen Sie eine begrenzte Ressource dar.</span><span class="sxs-lookup"><span data-stu-id="cd438-108">Represent some limited resource.</span></span>
- <span data-ttu-id="cd438-109">Wird vorhersag Bar und häufig verwendet.</span><span class="sxs-lookup"><span data-stu-id="cd438-109">Used predictably and frequently.</span></span>

<span data-ttu-id="cd438-110">Beispielsweise verwendet das ASP.net Core Framework den Objekt Pool an manchen Stellen, um Instanzen wiederzuverwenden <xref:System.Text.StringBuilder> .</span><span class="sxs-lookup"><span data-stu-id="cd438-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="cd438-111">`StringBuilder` ordnet und verwaltet eigene Puffer zum Speichern von Zeichendaten.</span><span class="sxs-lookup"><span data-stu-id="cd438-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="cd438-112">ASP.net Core werden regelmäßig verwendet `StringBuilder` , um Funktionen zu implementieren, und die Wiederverwendung bietet einen Leistungsvorteil.</span><span class="sxs-lookup"><span data-stu-id="cd438-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="cd438-113">Das Objekt Pooling verbessert nicht immer die Leistung:</span><span class="sxs-lookup"><span data-stu-id="cd438-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="cd438-114">Wenn die Initialisierungs Kosten eines Objekts nicht hoch sind, ist es normalerweise langsamer, das Objekt aus dem Pool zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="cd438-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="cd438-115">Die Zuordnung von Objekten, die vom Pool verwaltet werden, wird erst aufgehoben, wenn die Zuordnung des Pools aufgehoben wird</span><span class="sxs-lookup"><span data-stu-id="cd438-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="cd438-116">Verwenden Sie das Objekt Pooling nur, nachdem Sie Leistungsdaten mithilfe realistischer Szenarien für Ihre APP oder Bibliothek gesammelt haben.</span><span class="sxs-lookup"><span data-stu-id="cd438-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="cd438-117">**Warnung: der `ObjectPool` implementiert nicht `IDisposable` . Es wird nicht empfohlen, es mit Typen zu verwenden, die eine Entsorgung benötigen.**</span><span class="sxs-lookup"><span data-stu-id="cd438-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span> <span data-ttu-id="cd438-118">`ObjectPool` in ASP.net Core 3,0 und höher unterstützt `IDisposable` .</span><span class="sxs-lookup"><span data-stu-id="cd438-118">`ObjectPool` in ASP.NET Core 3.0 and later supports `IDisposable`.</span></span>
::: moniker-end

<span data-ttu-id="cd438-119">**Hinweis: die Anzahl der Objekte, die Sie zuordnen soll, wird von Objectpool nicht begrenzt, und die Anzahl der Objekte, die Sie beibehält, ist begrenzt.**</span><span class="sxs-lookup"><span data-stu-id="cd438-119">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="cd438-120">Konzepte</span><span class="sxs-lookup"><span data-stu-id="cd438-120">Concepts</span></span>

<span data-ttu-id="cd438-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> : die grundlegende Objekt Pool Abstraktion.</span><span class="sxs-lookup"><span data-stu-id="cd438-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="cd438-122">Wird verwendet, um-Objekte zu erhalten und zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="cd438-122">Used to get and return objects.</span></span>

<span data-ttu-id="cd438-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -Implementieren Sie diese, um anzupassen, wie ein Objekt erstellt wird und wie es *zurückgesetzt* wird, wenn es an den Pool zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="cd438-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="cd438-124">Dies kann an einen Objekt Pool übermittelt werden, den Sie direkt erstellen... Noch</span><span class="sxs-lookup"><span data-stu-id="cd438-124">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="cd438-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> fungiert als Factory zum Erstellen von Objekt Pools.</span><span class="sxs-lookup"><span data-stu-id="cd438-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="cd438-126">Der Objectpool kann in einer APP auf verschiedene Weise verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="cd438-126">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="cd438-127">Instanziieren eines Pools.</span><span class="sxs-lookup"><span data-stu-id="cd438-127">Instantiating a pool.</span></span>
* <span data-ttu-id="cd438-128">Registrieren eines Pools in [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) als-Instanz.</span><span class="sxs-lookup"><span data-stu-id="cd438-128">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="cd438-129">Registrieren von `ObjectPoolProvider<>` in di und Verwendung als Factory.</span><span class="sxs-lookup"><span data-stu-id="cd438-129">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="cd438-130">Verwenden von Objectpool</span><span class="sxs-lookup"><span data-stu-id="cd438-130">How to use ObjectPool</span></span>

<span data-ttu-id="cd438-131">Ruft <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> auf, um ein-Objekt abzurufen und <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> das-Objekt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="cd438-131">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="cd438-132">Es ist nicht erforderlich, jedes Objekt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="cd438-132">There's no requirement that you return every object.</span></span> <span data-ttu-id="cd438-133">Wenn Sie kein Objekt zurückgeben, wird die Garbage Collection durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="cd438-133">If you don't return an object, it will be garbage collected.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="cd438-134">Wenn <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> verwendet wird und `T` implementiert `IDisposable` :</span><span class="sxs-lookup"><span data-stu-id="cd438-134">When <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> is used and `T` implements `IDisposable`:</span></span>

* <span data-ttu-id="cd438-135">Elemente, die \* **Not** _ sind, werden an den Pool zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cd438-135">Items that are \* **not** _ returned to the pool will be disposed.</span></span>
<span data-ttu-id="cd438-136">_ Wenn der Pool von di verworfen wird, werden alle Elemente im Pool verworfen.</span><span class="sxs-lookup"><span data-stu-id="cd438-136">_ When the pool gets disposed by DI, all items in the pool are disposed.</span></span>

<span data-ttu-id="cd438-137">Hinweis: nach dem verwerfen des Pools:</span><span class="sxs-lookup"><span data-stu-id="cd438-137">NOTE: After the pool is disposed:</span></span>

* <span data-ttu-id="cd438-138">Durch Aufrufen von wird `Get` eine ausgelöst `ObjectDisposedException` .</span><span class="sxs-lookup"><span data-stu-id="cd438-138">Calling `Get` throws a `ObjectDisposedException`.</span></span>
* <span data-ttu-id="cd438-139">`return` Gibt das angegebene Element frei.</span><span class="sxs-lookup"><span data-stu-id="cd438-139">`return` disposes the given item.</span></span>

::: moniker-end

## <a name="objectpool-sample"></a><span data-ttu-id="cd438-140">Objectpool-Beispiel</span><span class="sxs-lookup"><span data-stu-id="cd438-140">ObjectPool sample</span></span>

<span data-ttu-id="cd438-141">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="cd438-141">The following code:</span></span>

* <span data-ttu-id="cd438-142">Fügt `ObjectPoolProvider` dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (di) hinzu.</span><span class="sxs-lookup"><span data-stu-id="cd438-142">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="cd438-143">Hiermit wird der di-Container hinzugefügt und konfiguriert `ObjectPool<StringBuilder>` .</span><span class="sxs-lookup"><span data-stu-id="cd438-143">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="cd438-144">Fügt hinzu `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="cd438-144">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="cd438-145">Der folgende Code implementiert `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="cd438-145">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
