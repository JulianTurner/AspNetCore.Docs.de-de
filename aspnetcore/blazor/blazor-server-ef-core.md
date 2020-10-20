---
title: ASP.NET Core Blazor Server mit Entity Framework Core (EFCore)
author: JeremyLikness
description: Leitfaden zum Verwenden von EF Core in Blazor Server-Apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
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
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: fc902cb5a82fda9fdbed09c40d66a846d9360f6a
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900738"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="02d5b-103">ASP.NET Core Blazor Server mit Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="02d5b-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="02d5b-104">Von: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="02d5b-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="02d5b-105">Blazor Server ist ein zustandsbehaftetes App-Framework.</span><span class="sxs-lookup"><span data-stu-id="02d5b-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="02d5b-106">Die App unterhält eine kontinuierliche Verbindung mit dem Server, und der Zustand des Benutzers wird im Arbeitsspeicher des Servers in einer *Verbindung* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="02d5b-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="02d5b-107">Ein Beispiel für den Benutzerzustand sind Daten, die in Dienstinstanzen mit [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection) gespeichert sind, die auf die Verbindung beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="02d5b-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="02d5b-108">Das eindeutige Anwendungsmodell, das Blazor Server bereitstellt, erfordert eine besondere Vorgehensweise bei der Verwendung von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="02d5b-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="02d5b-109">In diesem Artikel wird EF Core in Blazor Server-Apps behandelt.</span><span class="sxs-lookup"><span data-stu-id="02d5b-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="02d5b-110">Blazor WebAssembly-Apps werden in einer WebAssembly-Sandbox ausgeführt, die die meisten direkten Datenbankverbindungen verhindert.</span><span class="sxs-lookup"><span data-stu-id="02d5b-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="02d5b-111">Die Ausführung von EF Core in Blazor WebAssembly sprengt jedoch den Rahmen dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="02d5b-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="02d5b-112">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="02d5b-112">Sample app</span></span></h2>

<span data-ttu-id="02d5b-113">Die Beispiel-App wurde als Referenz für Blazor Server-Apps erstellt, die EF Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="02d5b-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="02d5b-114">Die Beispiel-App enthält ein Raster mit Sortier- und Filter-, Lösch-, Hinzufügungs- und Aktualisierungsvorgängen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="02d5b-115">Das Beispiel veranschaulicht die Verwendung von EF Core zum Verarbeiten vollständiger Parallelität.</span><span class="sxs-lookup"><span data-stu-id="02d5b-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="02d5b-116">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02d5b-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="02d5b-117">Das Beispiel verwendet eine lokale [SQLite](https://www.sqlite.org/index.html)-Datenbank, sodass es auf jeder Plattform verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="02d5b-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="02d5b-118">Das Beispiel konfiguriert auch die Datenbankprotokollierung, um die generierten SQL-Abfragen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="02d5b-119">Dies wird in `appsettings.Development.json` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="02d5b-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="02d5b-120">Die Raster-, Hinzufügungs- und Anzeigekomponenten verwenden das Muster „Kontext pro Vorgang“, bei dem ein Kontext für jeden Vorgang erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="02d5b-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="02d5b-121">Die Bearbeitungskomponente verwendet das Muster „Kontext pro Komponente“, bei dem ein Kontext für jede Komponente erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="02d5b-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="02d5b-122">Einige der Codebeispiele in diesem Thema erfordern Namespaces und Dienste, die nicht gezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="02d5b-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="02d5b-123">Um den voll funktionsfähigen Code einschließlich der erforderlichen [`@using`](xref:mvc/views/razor#using)- und [`@inject`](xref:mvc/views/razor#inject)-Anweisungen für Razor-Beispiele zu untersuchen, ziehen Sie die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) zu Rate.</span><span class="sxs-lookup"><span data-stu-id="02d5b-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="02d5b-124">Datenbankzugriff</span><span class="sxs-lookup"><span data-stu-id="02d5b-124">Database access</span></span></h2>

<span data-ttu-id="02d5b-125">EF Core stützt sich auf einen <xref:Microsoft.EntityFrameworkCore.DbContext> als Mittel zur [Konfiguration des Datenbankzugriffs](/ef/core/miscellaneous/configuring-dbcontext) und um als [*Arbeitseinheit*](https://martinfowler.com/eaaCatalog/unitOfWork.html) zu fungieren.</span><span class="sxs-lookup"><span data-stu-id="02d5b-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="02d5b-126">EF Core stellt die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterung für ASP.NET Core-Apps bereit, die den Kontext standardmäßig als *bereichsbezogenen* Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="02d5b-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="02d5b-127">In Blazor Server-Apps können bereichsbezogene Dienstregistrierungen problematisch sein, da die Instanz innerhalb der Verbindung des Benutzers von allen Komponenten gemeinsam genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="02d5b-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="02d5b-128"><xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und nicht für gleichzeitige Verwendung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="02d5b-129">Die vorhandenen Lebensdauern sind aus den folgenden Gründen ungeeignet:</span><span class="sxs-lookup"><span data-stu-id="02d5b-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="02d5b-130">**Singleton** verwendet den Zustand für alle Benutzer der App und führt zu einer ungeeigneten gleichzeitigen Verwendung.</span><span class="sxs-lookup"><span data-stu-id="02d5b-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="02d5b-131">**Scoped (Bereichsbezogen)** (die Standardeinstellung) stellt ein ähnliches Problem zwischen Komponenten für denselben Benutzer dar.</span><span class="sxs-lookup"><span data-stu-id="02d5b-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="02d5b-132">**Transient (Vorübergehend)** führt zu einer neuen Instanz pro Anforderung. Da Komponenten jedoch langlebig sein können, führt dies zu einem längerlebigen Kontext als möglicherweise beabsichtigt.</span><span class="sxs-lookup"><span data-stu-id="02d5b-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="02d5b-133">Die folgenden Empfehlungen sollen einen konsistenten Ansatz für die Verwendung von EF Core in Blazor Server-Apps bieten.</span><span class="sxs-lookup"><span data-stu-id="02d5b-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="02d5b-134">Erwägen Sie standardmäßig die Verwendung eines Kontexts pro Vorgang.</span><span class="sxs-lookup"><span data-stu-id="02d5b-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="02d5b-135">Der Kontext ist für schnelle Instanziierung mit geringem Mehraufwand konzipiert:</span><span class="sxs-lookup"><span data-stu-id="02d5b-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="02d5b-136">Verwenden Sie ein Flag, um mehrere gleichzeitige Vorgänge zu verhindern:</span><span class="sxs-lookup"><span data-stu-id="02d5b-136">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="02d5b-137">Platzieren Sie Vorgänge nach der Zeile `Loading = true;` im `try`-Block.</span><span class="sxs-lookup"><span data-stu-id="02d5b-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="02d5b-138">Bei langlebigen Vorgängen, die die Vorteile der [Änderungsnachverfolgung](/ef/core/querying/tracking) oder [Parallelitätssteuerung](/ef/core/saving/concurrency) von EF Core nutzen, [passen Sie den Kontext an die Lebensdauer der Komponente an](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="02d5b-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="02d5b-139">Neue DbContext-Instanzen</span><span class="sxs-lookup"><span data-stu-id="02d5b-139">New DbContext instances</span></span></h3>

<span data-ttu-id="02d5b-140">Die schnellste Möglichkeit zum Erstellen einer neuen <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz ist die Verwendung von `new`, um eine neue Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="02d5b-141">Es gibt jedoch mehrere Szenarien, in denen möglicherweise zusätzliche Abhängigkeiten aufgelöst werden müssen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="02d5b-142">Beispielsweise möchten Sie möglicherweise [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) verwenden, um den Kontext zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="02d5b-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="02d5b-143">Die empfohlene Lösung zum Erstellen eines neuen <xref:Microsoft.EntityFrameworkCore.DbContext> mit Abhängigkeiten ist die Verwendung einer Factory.</span><span class="sxs-lookup"><span data-stu-id="02d5b-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="02d5b-144">EF Core 5.0 oder höher bietet eine integrierte Factory zum Erstellen neuer Kontexte.</span><span class="sxs-lookup"><span data-stu-id="02d5b-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="02d5b-145">Im folgenden Beispiel wird [SQLite](https://www.sqlite.org/index.html) konfiguriert und Datenprotokollierung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="02d5b-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="02d5b-146">Der Code verwendet eine [Erweiterungsmethode (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) zum Konfigurieren der Datenbankfactory für DI und bietet Standardoptionen:</span><span class="sxs-lookup"><span data-stu-id="02d5b-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="02d5b-147">Die Factory wird in Komponenten eingefügt und zum Erstellen neuer Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="02d5b-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="02d5b-148">Gehen Sie beispielsweise in `Pages/Index.razor` folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="02d5b-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="02d5b-149">`Wrapper` ist eine [Komponentenreferenz](xref:blazor/components/index#capture-references-to-components) für die `GridWrapper`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="02d5b-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="02d5b-150">Sehen Sie sich dazu die `Index`-Komponente (`Pages/Index.razor`) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor) an.</span><span class="sxs-lookup"><span data-stu-id="02d5b-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="02d5b-151">Gültigkeitsbereich für die Komponentenlebensdauer</span><span class="sxs-lookup"><span data-stu-id="02d5b-151">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="02d5b-152">Möglicherweise möchten Sie einen <xref:Microsoft.EntityFrameworkCore.DbContext> erstellen, der für die Lebensdauer einer Komponente vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="02d5b-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="02d5b-153">Dies ermöglicht Ihnen die Verwendung als [Arbeitseinheit](https://martinfowler.com/eaaCatalog/unitOfWork.html) und nutzt integrierte Features, z. B. Änderungsnachverfolgung und Parallelitätsauflösung.</span><span class="sxs-lookup"><span data-stu-id="02d5b-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="02d5b-154">Sie können die Factory verwenden, um einen Kontext zu erstellen und diesen für die gesamte Lebensdauer der Komponente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="02d5b-155">Implementieren Sie zunächst <xref:System.IDisposable>, und fügen Sie die Factory dann wie in `Pages/EditContact.razor` gezeigt ein:</span><span class="sxs-lookup"><span data-stu-id="02d5b-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="02d5b-156">Die Beispiel-App stellt sicher, dass der Kontext verworfen wird, wenn die Komponente verworfen wird:</span><span class="sxs-lookup"><span data-stu-id="02d5b-156">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="02d5b-157">Schließlich wird [`OnInitializedAsync`](xref:blazor/components/lifecycle) überschrieben, um einen neuen Kontext zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="02d5b-158">In der Beispiel-App lädt [`OnInitializedAsync`](xref:blazor/components/lifecycle) den Kontakt in derselben Methode:</span><span class="sxs-lookup"><span data-stu-id="02d5b-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="02d5b-159">Blazor Server ist ein zustandsbehaftetes App-Framework.</span><span class="sxs-lookup"><span data-stu-id="02d5b-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="02d5b-160">Die App unterhält eine kontinuierliche Verbindung mit dem Server, und der Zustand des Benutzers wird im Arbeitsspeicher des Servers in einer *Verbindung* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="02d5b-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="02d5b-161">Ein Beispiel für den Benutzerzustand sind Daten, die in Dienstinstanzen mit [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection) gespeichert sind, die auf die Verbindung beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="02d5b-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="02d5b-162">Das eindeutige Anwendungsmodell, das Blazor Server bereitstellt, erfordert eine besondere Vorgehensweise bei der Verwendung von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="02d5b-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="02d5b-163">In diesem Artikel wird EF Core in Blazor Server-Apps behandelt.</span><span class="sxs-lookup"><span data-stu-id="02d5b-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="02d5b-164">Blazor WebAssembly-Apps werden in einer WebAssembly-Sandbox ausgeführt, die die meisten direkten Datenbankverbindungen verhindert.</span><span class="sxs-lookup"><span data-stu-id="02d5b-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="02d5b-165">Die Ausführung von EF Core in Blazor WebAssembly sprengt jedoch den Rahmen dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="02d5b-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="02d5b-166">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="02d5b-166">Sample app</span></span></h2>

<span data-ttu-id="02d5b-167">Die Beispiel-App wurde als Referenz für Blazor Server-Apps erstellt, die EF Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="02d5b-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="02d5b-168">Die Beispiel-App enthält ein Raster mit Sortier- und Filter-, Lösch-, Hinzufügungs- und Aktualisierungsvorgängen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="02d5b-169">Das Beispiel veranschaulicht die Verwendung von EF Core zum Verarbeiten vollständiger Parallelität.</span><span class="sxs-lookup"><span data-stu-id="02d5b-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="02d5b-170">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="02d5b-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="02d5b-171">Das Beispiel verwendet eine lokale [SQLite](https://www.sqlite.org/index.html)-Datenbank, sodass es auf jeder Plattform verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="02d5b-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="02d5b-172">Das Beispiel konfiguriert auch die Datenbankprotokollierung, um die generierten SQL-Abfragen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="02d5b-173">Dies wird in `appsettings.Development.json` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="02d5b-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="02d5b-174">Die Raster-, Hinzufügungs- und Anzeigekomponenten verwenden das Muster „Kontext pro Vorgang“, bei dem ein Kontext für jeden Vorgang erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="02d5b-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="02d5b-175">Die Bearbeitungskomponente verwendet das Muster „Kontext pro Komponente“, bei dem ein Kontext für jede Komponente erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="02d5b-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="02d5b-176">Einige der Codebeispiele in diesem Thema erfordern Namespaces und Dienste, die nicht gezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="02d5b-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="02d5b-177">Um den voll funktionsfähigen Code einschließlich der erforderlichen [`@using`](xref:mvc/views/razor#using)- und [`@inject`](xref:mvc/views/razor#inject)-Anweisungen für Razor-Beispiele zu untersuchen, ziehen Sie die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) zu Rate.</span><span class="sxs-lookup"><span data-stu-id="02d5b-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="02d5b-178">Datenbankzugriff</span><span class="sxs-lookup"><span data-stu-id="02d5b-178">Database access</span></span></h2>

<span data-ttu-id="02d5b-179">EF Core stützt sich auf einen <xref:Microsoft.EntityFrameworkCore.DbContext> als Mittel zur [Konfiguration des Datenbankzugriffs](/ef/core/miscellaneous/configuring-dbcontext) und um als [*Arbeitseinheit*](https://martinfowler.com/eaaCatalog/unitOfWork.html) zu fungieren.</span><span class="sxs-lookup"><span data-stu-id="02d5b-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="02d5b-180">EF Core stellt die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterung für ASP.NET Core-Apps bereit, die den Kontext standardmäßig als *bereichsbezogenen* Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="02d5b-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="02d5b-181">In Blazor Server-Apps kann dies problematisch sein, da die Instanz innerhalb der Verbindung des Benutzers von allen Komponenten gemeinsam genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="02d5b-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="02d5b-182"><xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und nicht für gleichzeitige Verwendung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="02d5b-183">Die vorhandenen Lebensdauern sind aus den folgenden Gründen ungeeignet:</span><span class="sxs-lookup"><span data-stu-id="02d5b-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="02d5b-184">**Singleton** verwendet den Zustand für alle Benutzer der App und führt zu einer ungeeigneten gleichzeitigen Verwendung.</span><span class="sxs-lookup"><span data-stu-id="02d5b-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="02d5b-185">**Scoped (Bereichsbezogen)** (die Standardeinstellung) stellt ein ähnliches Problem zwischen Komponenten für denselben Benutzer dar.</span><span class="sxs-lookup"><span data-stu-id="02d5b-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="02d5b-186">**Transient (Vorübergehend)** führt zu einer neuen Instanz pro Anforderung. Da Komponenten jedoch langlebig sein können, führt dies zu einem längerlebigen Kontext als möglicherweise beabsichtigt.</span><span class="sxs-lookup"><span data-stu-id="02d5b-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="02d5b-187">Die folgenden Empfehlungen sollen einen konsistenten Ansatz für die Verwendung von EF Core in Blazor Server-Apps bieten.</span><span class="sxs-lookup"><span data-stu-id="02d5b-187">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="02d5b-188">Erwägen Sie standardmäßig die Verwendung eines Kontexts pro Vorgang.</span><span class="sxs-lookup"><span data-stu-id="02d5b-188">By default, consider using one context per operation.</span></span> <span data-ttu-id="02d5b-189">Der Kontext ist für schnelle Instanziierung mit geringem Mehraufwand konzipiert:</span><span class="sxs-lookup"><span data-stu-id="02d5b-189">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="02d5b-190">Verwenden Sie ein Flag, um mehrere gleichzeitige Vorgänge zu verhindern:</span><span class="sxs-lookup"><span data-stu-id="02d5b-190">Use a flag to prevent multiple concurrent operations:</span></span>

  ```csharp
  if (Loading)
  {
      return;
  }

  try
  {
      Loading = true;

      ...
  }
  finally
  {
      Loading = false;
  }
  ```

  <span data-ttu-id="02d5b-191">Platzieren Sie Vorgänge nach der Zeile `Loading = true;` im `try`-Block.</span><span class="sxs-lookup"><span data-stu-id="02d5b-191">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="02d5b-192">Bei langlebigen Vorgängen, die die Vorteile der [Änderungsnachverfolgung](/ef/core/querying/tracking) oder [Parallelitätssteuerung](/ef/core/saving/concurrency) von EF Core nutzen, [passen Sie den Kontext an die Lebensdauer der Komponente an](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="02d5b-192">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="02d5b-193">Neue DbContext-Instanzen</span><span class="sxs-lookup"><span data-stu-id="02d5b-193">New DbContext instances</span></span></h3>

<span data-ttu-id="02d5b-194">Die schnellste Möglichkeit zum Erstellen einer neuen <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz ist die Verwendung von `new`, um eine neue Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-194">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="02d5b-195">Es gibt jedoch mehrere Szenarien, in denen möglicherweise zusätzliche Abhängigkeiten aufgelöst werden müssen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-195">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="02d5b-196">Beispielsweise möchten Sie möglicherweise [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) verwenden, um den Kontext zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="02d5b-196">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="02d5b-197">Die empfohlene Lösung zum Erstellen eines neuen <xref:Microsoft.EntityFrameworkCore.DbContext> mit Abhängigkeiten ist die Verwendung einer Factory.</span><span class="sxs-lookup"><span data-stu-id="02d5b-197">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="02d5b-198">Die Beispiel-App implementiert ihre eigene Factory in `Data/DbContextFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="02d5b-198">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="02d5b-199">In der vorhergehenden Factory gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="02d5b-199">In the preceding factory:</span></span>

* <span data-ttu-id="02d5b-200"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> erfüllt alle Abhängigkeiten über den Dienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="02d5b-200"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="02d5b-201">`IDbContextFactory` ist in EF Core ASP.NET Core 5.0 oder höher verfügbar, sodass die Schnittstelle [in der Beispiel-App für ASP.NET Core 3.x implementiert](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs) ist.</span><span class="sxs-lookup"><span data-stu-id="02d5b-201">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="02d5b-202">Im folgenden Beispiel wird [SQLite](https://www.sqlite.org/index.html) konfiguriert und Datenprotokollierung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="02d5b-202">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="02d5b-203">Der Code verwendet eine Erweiterungsmethode zum Konfigurieren der Datenbankfactory für DI und bietet Standardoptionen:</span><span class="sxs-lookup"><span data-stu-id="02d5b-203">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="02d5b-204">Die Factory wird in Komponenten eingefügt und zum Erstellen neuer Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="02d5b-204">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="02d5b-205">Gehen Sie beispielsweise in `Pages/Index.razor` folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="02d5b-205">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="02d5b-206">`Wrapper` ist eine [Komponentenreferenz](xref:blazor/components/index#capture-references-to-components) für die `GridWrapper`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="02d5b-206">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="02d5b-207">Sehen Sie sich dazu die `Index`-Komponente (`Pages/Index.razor`) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor) an.</span><span class="sxs-lookup"><span data-stu-id="02d5b-207">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="02d5b-208">Gültigkeitsbereich für die Komponentenlebensdauer</span><span class="sxs-lookup"><span data-stu-id="02d5b-208">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="02d5b-209">Möglicherweise möchten Sie einen <xref:Microsoft.EntityFrameworkCore.DbContext> erstellen, der für die Lebensdauer einer Komponente vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="02d5b-209">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="02d5b-210">Dies ermöglicht Ihnen die Verwendung als [Arbeitseinheit](https://martinfowler.com/eaaCatalog/unitOfWork.html) und nutzt integrierte Features, z. B. Änderungsnachverfolgung und Parallelitätsauflösung.</span><span class="sxs-lookup"><span data-stu-id="02d5b-210">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="02d5b-211">Sie können die Factory verwenden, um einen Kontext zu erstellen und diesen für die gesamte Lebensdauer der Komponente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-211">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="02d5b-212">Implementieren Sie zunächst <xref:System.IDisposable>, und fügen Sie die Factory dann wie in `Pages/EditContact.razor` gezeigt ein:</span><span class="sxs-lookup"><span data-stu-id="02d5b-212">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="02d5b-213">Die Beispiel-App stellt sicher, dass der Kontext verworfen wird, wenn die Komponente verworfen wird:</span><span class="sxs-lookup"><span data-stu-id="02d5b-213">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="02d5b-214">Schließlich wird [`OnInitializedAsync`](xref:blazor/components/lifecycle) überschrieben, um einen neuen Kontext zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-214">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="02d5b-215">In der Beispiel-App lädt [`OnInitializedAsync`](xref:blazor/components/lifecycle) den Kontakt in derselben Methode:</span><span class="sxs-lookup"><span data-stu-id="02d5b-215">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="02d5b-216">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="02d5b-216">In the preceding example:</span></span>

* <span data-ttu-id="02d5b-217">Wenn `Busy` auf `true` festgelegt ist, können asynchrone Vorgänge beginnen.</span><span class="sxs-lookup"><span data-stu-id="02d5b-217">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="02d5b-218">Wenn `Busy` auf `false` zurückgesetzt wird, sollten asynchrone Vorgänge abgeschlossen sein.</span><span class="sxs-lookup"><span data-stu-id="02d5b-218">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="02d5b-219">Fügen Sie zusätzliche Fehlerbehandlungslogik in einen `catch`-Block ein.</span><span class="sxs-lookup"><span data-stu-id="02d5b-219">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="02d5b-220">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="02d5b-220">Additional resources</span></span>

* [<span data-ttu-id="02d5b-221">EF Core-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="02d5b-221">EF Core documentation</span></span>](/ef/)
