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
ms.openlocfilehash: b71b742c8a60b4b563649baa181b8c332ff02501
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865195"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="4fe85-103">ASP.NET Core Blazor Server mit Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="4fe85-103">ASP.NET Core Blazor Server with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="4fe85-104">Von: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="4fe85-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4fe85-105">Blazor Server ist ein zustandsbehaftetes App-Framework.</span><span class="sxs-lookup"><span data-stu-id="4fe85-105">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="4fe85-106">Die App unterhält eine kontinuierliche Verbindung mit dem Server, und der Zustand des Benutzers wird im Arbeitsspeicher des Servers in einer *Verbindung* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="4fe85-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="4fe85-107">Ein Beispiel für den Benutzerzustand sind Daten, die in Dienstinstanzen mit [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection) gespeichert sind, die auf die Verbindung beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="4fe85-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="4fe85-108">Das eindeutige Anwendungsmodell, das Blazor Server bereitstellt, erfordert eine besondere Vorgehensweise bei der Verwendung von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="4fe85-108">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="4fe85-109">In diesem Artikel wird EF Core in Blazor Server-Apps behandelt.</span><span class="sxs-lookup"><span data-stu-id="4fe85-109">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="4fe85-110">Blazor WebAssembly-Apps werden in einer WebAssembly-Sandbox ausgeführt, die die meisten direkten Datenbankverbindungen verhindert.</span><span class="sxs-lookup"><span data-stu-id="4fe85-110">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="4fe85-111">Die Ausführung von EF Core in Blazor WebAssembly sprengt jedoch den Rahmen dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="4fe85-111">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="4fe85-112">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="4fe85-112">Sample app</span></span>

<span data-ttu-id="4fe85-113">Die Beispiel-App wurde als Referenz für Blazor Server-Apps erstellt, die EF Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="4fe85-113">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="4fe85-114">Die Beispiel-App enthält ein Raster mit Sortier- und Filter-, Lösch-, Hinzufügungs- und Aktualisierungsvorgängen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="4fe85-115">Das Beispiel veranschaulicht die Verwendung von EF Core zum Verarbeiten vollständiger Parallelität.</span><span class="sxs-lookup"><span data-stu-id="4fe85-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="4fe85-116">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4fe85-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4fe85-117">Das Beispiel verwendet eine lokale [SQLite](https://www.sqlite.org/index.html)-Datenbank, sodass es auf jeder Plattform verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="4fe85-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="4fe85-118">Das Beispiel konfiguriert auch die Datenbankprotokollierung, um die generierten SQL-Abfragen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="4fe85-119">Dies wird in `appsettings.Development.json` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="4fe85-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="4fe85-120">Die Raster-, Hinzufügungs- und Anzeigekomponenten verwenden das Muster „Kontext pro Vorgang“, bei dem ein Kontext für jeden Vorgang erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4fe85-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="4fe85-121">Die Bearbeitungskomponente verwendet das Muster „Kontext pro Komponente“, bei dem ein Kontext für jede Komponente erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4fe85-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="4fe85-122">Einige der Codebeispiele in diesem Thema erfordern Namespaces und Dienste, die nicht gezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="4fe85-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="4fe85-123">Um den voll funktionsfähigen Code einschließlich der erforderlichen [`@using`](xref:mvc/views/razor#using)- und [`@inject`](xref:mvc/views/razor#inject)-Anweisungen für Razor-Beispiele zu untersuchen, ziehen Sie die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) zu Rate.</span><span class="sxs-lookup"><span data-stu-id="4fe85-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="4fe85-124">Datenbankzugriff</span><span class="sxs-lookup"><span data-stu-id="4fe85-124">Database access</span></span>

<span data-ttu-id="4fe85-125">EF Core stützt sich auf einen <xref:Microsoft.EntityFrameworkCore.DbContext> als Mittel zur [Konfiguration des Datenbankzugriffs](/ef/core/miscellaneous/configuring-dbcontext) und um als [*Arbeitseinheit*](https://martinfowler.com/eaaCatalog/unitOfWork.html) zu fungieren.</span><span class="sxs-lookup"><span data-stu-id="4fe85-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="4fe85-126">EF Core stellt die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterung für ASP.NET Core-Apps bereit, die den Kontext standardmäßig als *bereichsbezogenen* Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="4fe85-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="4fe85-127">In Blazor Server-Apps können bereichsbezogene Dienstregistrierungen problematisch sein, da die Instanz innerhalb der Verbindung des Benutzers von allen Komponenten gemeinsam genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="4fe85-127">In Blazor Server apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="4fe85-128"><xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und nicht für gleichzeitige Verwendung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="4fe85-129">Die vorhandenen Lebensdauern sind aus den folgenden Gründen ungeeignet:</span><span class="sxs-lookup"><span data-stu-id="4fe85-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="4fe85-130">**Singleton** verwendet den Zustand für alle Benutzer der App und führt zu einer ungeeigneten gleichzeitigen Verwendung.</span><span class="sxs-lookup"><span data-stu-id="4fe85-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="4fe85-131">**Scoped (Bereichsbezogen)** (die Standardeinstellung) stellt ein ähnliches Problem zwischen Komponenten für denselben Benutzer dar.</span><span class="sxs-lookup"><span data-stu-id="4fe85-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="4fe85-132">**Transient (Vorübergehend)** führt zu einer neuen Instanz pro Anforderung. Da Komponenten jedoch langlebig sein können, führt dies zu einem längerlebigen Kontext als möglicherweise beabsichtigt.</span><span class="sxs-lookup"><span data-stu-id="4fe85-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="4fe85-133">Die folgenden Empfehlungen sollen einen konsistenten Ansatz für die Verwendung von EF Core in Blazor Server-Apps bieten.</span><span class="sxs-lookup"><span data-stu-id="4fe85-133">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="4fe85-134">Erwägen Sie standardmäßig die Verwendung eines Kontexts pro Vorgang.</span><span class="sxs-lookup"><span data-stu-id="4fe85-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="4fe85-135">Der Kontext ist für schnelle Instanziierung mit geringem Mehraufwand konzipiert:</span><span class="sxs-lookup"><span data-stu-id="4fe85-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="4fe85-136">Verwenden Sie ein Flag, um mehrere gleichzeitige Vorgänge zu verhindern:</span><span class="sxs-lookup"><span data-stu-id="4fe85-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="4fe85-137">Platzieren Sie Vorgänge nach der Zeile `Loading = true;` im `try`-Block.</span><span class="sxs-lookup"><span data-stu-id="4fe85-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="4fe85-138">Bei langlebigen Vorgängen, die die Vorteile der [Änderungsnachverfolgung](/ef/core/querying/tracking) oder [Parallelitätssteuerung](/ef/core/saving/concurrency) von EF Core nutzen, [passen Sie den Kontext an die Lebensdauer der Komponente an](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="4fe85-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="new-dbcontext-instances"></a><span data-ttu-id="4fe85-139">Neue DbContext-Instanzen</span><span class="sxs-lookup"><span data-stu-id="4fe85-139">New DbContext instances</span></span>

<span data-ttu-id="4fe85-140">Die schnellste Möglichkeit zum Erstellen einer neuen <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz ist die Verwendung von `new`, um eine neue Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="4fe85-141">Es gibt jedoch mehrere Szenarien, in denen möglicherweise zusätzliche Abhängigkeiten aufgelöst werden müssen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="4fe85-142">Beispielsweise möchten Sie möglicherweise [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) verwenden, um den Kontext zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="4fe85-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="4fe85-143">Die empfohlene Lösung zum Erstellen eines neuen <xref:Microsoft.EntityFrameworkCore.DbContext> mit Abhängigkeiten ist die Verwendung einer Factory.</span><span class="sxs-lookup"><span data-stu-id="4fe85-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="4fe85-144">EF Core 5.0 oder höher bietet eine integrierte Factory zum Erstellen neuer Kontexte.</span><span class="sxs-lookup"><span data-stu-id="4fe85-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="4fe85-145">Im folgenden Beispiel wird [SQLite](https://www.sqlite.org/index.html) konfiguriert und Datenprotokollierung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="4fe85-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="4fe85-146">Der Code verwendet eine Erweiterungsmethode zum Konfigurieren der Datenbankfactory für DI und bietet Standardoptionen:</span><span class="sxs-lookup"><span data-stu-id="4fe85-146">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="4fe85-147">Die Factory wird in Komponenten eingefügt und zum Erstellen neuer Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="4fe85-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="4fe85-148">Gehen Sie beispielsweise in `Pages/Index.razor` folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="4fe85-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> <span data-ttu-id="4fe85-149">![HINWEIS] `Wrapper` ist eine [Komponentenreferenz](xref:blazor/components/index#capture-references-to-components) für die `GridWrapper`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="4fe85-149">![NOTE] `Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="4fe85-150">Sehen Sie sich dazu die `Index`-Komponente (`Pages/Index.razor`) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor) an.</span><span class="sxs-lookup"><span data-stu-id="4fe85-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="4fe85-151">Gültigkeitsbereich für die Komponentenlebensdauer</span><span class="sxs-lookup"><span data-stu-id="4fe85-151">Scope to the component lifetime</span></span>

<span data-ttu-id="4fe85-152">Möglicherweise möchten Sie einen <xref:Microsoft.EntityFrameworkCore.DbContext> erstellen, der für die Lebensdauer einer Komponente vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4fe85-152">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="4fe85-153">Dies ermöglicht Ihnen die Verwendung als [Arbeitseinheit](https://martinfowler.com/eaaCatalog/unitOfWork.html) und nutzt integrierte Features, z. B. Änderungsnachverfolgung und Parallelitätsauflösung.</span><span class="sxs-lookup"><span data-stu-id="4fe85-153">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="4fe85-154">Sie können die Factory verwenden, um einen Kontext zu erstellen und diesen für die gesamte Lebensdauer der Komponente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-154">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="4fe85-155">Implementieren Sie zunächst <xref:System.IDisposable>, und fügen Sie die Factory dann wie in `Pages/EditContact.razor` gezeigt ein:</span><span class="sxs-lookup"><span data-stu-id="4fe85-155">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="4fe85-156">Die Beispiel-App stellt sicher, dass der Kontakt verworfen wird, wenn die Komponente verworfen wird:</span><span class="sxs-lookup"><span data-stu-id="4fe85-156">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="4fe85-157">Schließlich wird [`OnInitializedAsync`](xref:blazor/components/lifecycle) überschrieben, um einen neuen Kontext zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-157">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="4fe85-158">In der Beispiel-App lädt [`OnInitializedAsync`](xref:blazor/components/lifecycle) den Kontakt in derselben Methode:</span><span class="sxs-lookup"><span data-stu-id="4fe85-158">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="4fe85-159">Blazor Server ist ein zustandsbehaftetes App-Framework.</span><span class="sxs-lookup"><span data-stu-id="4fe85-159">Blazor Server is a stateful app framework.</span></span> <span data-ttu-id="4fe85-160">Die App unterhält eine kontinuierliche Verbindung mit dem Server, und der Zustand des Benutzers wird im Arbeitsspeicher des Servers in einer *Verbindung* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="4fe85-160">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit*.</span></span> <span data-ttu-id="4fe85-161">Ein Beispiel für den Benutzerzustand sind Daten, die in Dienstinstanzen mit [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection) gespeichert sind, die auf die Verbindung beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="4fe85-161">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="4fe85-162">Das eindeutige Anwendungsmodell, das Blazor Server bereitstellt, erfordert eine besondere Vorgehensweise bei der Verwendung von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="4fe85-162">The unique application model that Blazor Server provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="4fe85-163">In diesem Artikel wird EF Core in Blazor Server-Apps behandelt.</span><span class="sxs-lookup"><span data-stu-id="4fe85-163">This article addresses EF Core in Blazor Server apps.</span></span> <span data-ttu-id="4fe85-164">Blazor WebAssembly-Apps werden in einer WebAssembly-Sandbox ausgeführt, die die meisten direkten Datenbankverbindungen verhindert.</span><span class="sxs-lookup"><span data-stu-id="4fe85-164">Blazor WebAssembly apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="4fe85-165">Die Ausführung von EF Core in Blazor WebAssembly sprengt jedoch den Rahmen dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="4fe85-165">Running EF Core in Blazor WebAssembly is beyond the scope of this article.</span></span>

## <a name="sample-app"></a><span data-ttu-id="4fe85-166">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="4fe85-166">Sample app</span></span>

<span data-ttu-id="4fe85-167">Die Beispiel-App wurde als Referenz für Blazor Server-Apps erstellt, die EF Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="4fe85-167">The sample app was built as a reference for Blazor Server apps that use EF Core.</span></span> <span data-ttu-id="4fe85-168">Die Beispiel-App enthält ein Raster mit Sortier- und Filter-, Lösch-, Hinzufügungs- und Aktualisierungsvorgängen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-168">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="4fe85-169">Das Beispiel veranschaulicht die Verwendung von EF Core zum Verarbeiten vollständiger Parallelität.</span><span class="sxs-lookup"><span data-stu-id="4fe85-169">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="4fe85-170">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4fe85-170">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4fe85-171">Das Beispiel verwendet eine lokale [SQLite](https://www.sqlite.org/index.html)-Datenbank, sodass es auf jeder Plattform verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="4fe85-171">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="4fe85-172">Das Beispiel konfiguriert auch die Datenbankprotokollierung, um die generierten SQL-Abfragen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-172">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="4fe85-173">Dies wird in `appsettings.Development.json` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="4fe85-173">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="4fe85-174">Die Raster-, Hinzufügungs- und Anzeigekomponenten verwenden das Muster „Kontext pro Vorgang“, bei dem ein Kontext für jeden Vorgang erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4fe85-174">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="4fe85-175">Die Bearbeitungskomponente verwendet das Muster „Kontext pro Komponente“, bei dem ein Kontext für jede Komponente erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4fe85-175">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="4fe85-176">Einige der Codebeispiele in diesem Thema erfordern Namespaces und Dienste, die nicht gezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="4fe85-176">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="4fe85-177">Um den voll funktionsfähigen Code einschließlich der erforderlichen [`@using`](xref:mvc/views/razor#using)- und [`@inject`](xref:mvc/views/razor#inject)-Anweisungen für Razor-Beispiele zu untersuchen, ziehen Sie die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) zu Rate.</span><span class="sxs-lookup"><span data-stu-id="4fe85-177">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for Razor examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample).</span></span>

## <a name="database-access"></a><span data-ttu-id="4fe85-178">Datenbankzugriff</span><span class="sxs-lookup"><span data-stu-id="4fe85-178">Database access</span></span>

<span data-ttu-id="4fe85-179">EF Core stützt sich auf einen <xref:Microsoft.EntityFrameworkCore.DbContext> als Mittel zur [Konfiguration des Datenbankzugriffs](/ef/core/miscellaneous/configuring-dbcontext) und um als [*Arbeitseinheit*](https://martinfowler.com/eaaCatalog/unitOfWork.html) zu fungieren.</span><span class="sxs-lookup"><span data-stu-id="4fe85-179">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="4fe85-180">EF Core stellt die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterung für ASP.NET Core-Apps bereit, die den Kontext standardmäßig als *bereichsbezogenen* Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="4fe85-180">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="4fe85-181">In Blazor Server-Apps kann dies problematisch sein, da die Instanz innerhalb der Verbindung des Benutzers von allen Komponenten gemeinsam genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="4fe85-181">In Blazor Server apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="4fe85-182"><xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und nicht für gleichzeitige Verwendung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-182"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="4fe85-183">Die vorhandenen Lebensdauern sind aus den folgenden Gründen ungeeignet:</span><span class="sxs-lookup"><span data-stu-id="4fe85-183">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="4fe85-184">**Singleton** verwendet den Zustand für alle Benutzer der App und führt zu einer ungeeigneten gleichzeitigen Verwendung.</span><span class="sxs-lookup"><span data-stu-id="4fe85-184">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="4fe85-185">**Scoped (Bereichsbezogen)** (die Standardeinstellung) stellt ein ähnliches Problem zwischen Komponenten für denselben Benutzer dar.</span><span class="sxs-lookup"><span data-stu-id="4fe85-185">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="4fe85-186">**Transient (Vorübergehend)** führt zu einer neuen Instanz pro Anforderung. Da Komponenten jedoch langlebig sein können, führt dies zu einem längerlebigen Kontext als möglicherweise beabsichtigt.</span><span class="sxs-lookup"><span data-stu-id="4fe85-186">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

## <a name="database-access"></a><span data-ttu-id="4fe85-187">Datenbankzugriff</span><span class="sxs-lookup"><span data-stu-id="4fe85-187">Database access</span></span>

<span data-ttu-id="4fe85-188">Die folgenden Empfehlungen sollen einen konsistenten Ansatz für die Verwendung von EF Core in Blazor Server-Apps bieten.</span><span class="sxs-lookup"><span data-stu-id="4fe85-188">The following recommendations are designed to provide a consistent approach to using EF Core in Blazor Server apps.</span></span>

* <span data-ttu-id="4fe85-189">Erwägen Sie standardmäßig die Verwendung eines Kontexts pro Vorgang.</span><span class="sxs-lookup"><span data-stu-id="4fe85-189">By default, consider using one context per operation.</span></span> <span data-ttu-id="4fe85-190">Der Kontext ist für schnelle Instanziierung mit geringem Mehraufwand konzipiert:</span><span class="sxs-lookup"><span data-stu-id="4fe85-190">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="4fe85-191">Verwenden Sie ein Flag, um mehrere gleichzeitige Vorgänge zu verhindern:</span><span class="sxs-lookup"><span data-stu-id="4fe85-191">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="4fe85-192">Platzieren Sie Vorgänge nach der Zeile `Loading = true;` im `try`-Block.</span><span class="sxs-lookup"><span data-stu-id="4fe85-192">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="4fe85-193">Bei langlebigen Vorgängen, die die Vorteile der [Änderungsnachverfolgung](/ef/core/querying/tracking) oder [Parallelitätssteuerung](/ef/core/saving/concurrency) von EF Core nutzen, [passen Sie den Kontext an die Lebensdauer der Komponente an](#scope-to-the-component-lifetime).</span><span class="sxs-lookup"><span data-stu-id="4fe85-193">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime).</span></span>

### <a name="create-new-dbcontext-instances"></a><span data-ttu-id="4fe85-194">Erstellen neuer DbContext-Instanzen</span><span class="sxs-lookup"><span data-stu-id="4fe85-194">Create new DbContext instances</span></span>

<span data-ttu-id="4fe85-195">Die schnellste Möglichkeit zum Erstellen einer neuen <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz ist die Verwendung von `new`, um eine neue Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-195">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="4fe85-196">Es gibt jedoch mehrere Szenarien, in denen möglicherweise zusätzliche Abhängigkeiten aufgelöst werden müssen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-196">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="4fe85-197">Beispielsweise möchten Sie möglicherweise [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) verwenden, um den Kontext zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="4fe85-197">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="4fe85-198">Die empfohlene Lösung zum Erstellen eines neuen <xref:Microsoft.EntityFrameworkCore.DbContext> mit Abhängigkeiten ist die Verwendung einer Factory.</span><span class="sxs-lookup"><span data-stu-id="4fe85-198">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="4fe85-199">Die Beispiel-App implementiert ihre eigene Factory in `Data/DbContextFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="4fe85-199">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="4fe85-200">In der Factory oben erfüllt <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> alle Abhängigkeiten über den Dienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="4fe85-200">In the preceding factory, <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>

<span data-ttu-id="4fe85-201">Im folgenden Beispiel wird [SQLite](https://www.sqlite.org/index.html) konfiguriert und Datenprotokollierung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="4fe85-201">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="4fe85-202">Der Code verwendet eine Erweiterungsmethode zum Konfigurieren der Datenbankfactory für DI und bietet Standardoptionen:</span><span class="sxs-lookup"><span data-stu-id="4fe85-202">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="4fe85-203">Die Factory wird in Komponenten eingefügt und zum Erstellen neuer Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="4fe85-203">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="4fe85-204">Gehen Sie beispielsweise in `Pages/Index.razor` folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="4fe85-204">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> <span data-ttu-id="4fe85-205">![HINWEIS] `Wrapper` ist eine [Komponentenreferenz](xref:blazor/components/index#capture-references-to-components) für die `GridWrapper`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="4fe85-205">![NOTE] `Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="4fe85-206">Sehen Sie sich dazu die `Index`-Komponente (`Pages/Index.razor`) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor) an.</span><span class="sxs-lookup"><span data-stu-id="4fe85-206">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor).</span></span>

### <a name="scope-to-the-component-lifetime"></a><span data-ttu-id="4fe85-207">Gültigkeitsbereich für die Komponentenlebensdauer</span><span class="sxs-lookup"><span data-stu-id="4fe85-207">Scope to the component lifetime</span></span>

<span data-ttu-id="4fe85-208">Möglicherweise möchten Sie einen <xref:Microsoft.EntityFrameworkCore.DbContext> erstellen, der für die Lebensdauer einer Komponente vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4fe85-208">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="4fe85-209">Dies ermöglicht Ihnen die Verwendung als [Arbeitseinheit](https://martinfowler.com/eaaCatalog/unitOfWork.html) und nutzt integrierte Features, z. B. Änderungsnachverfolgung und Parallelitätsauflösung.</span><span class="sxs-lookup"><span data-stu-id="4fe85-209">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="4fe85-210">Sie können die Factory verwenden, um einen Kontext zu erstellen und diesen für die gesamte Lebensdauer der Komponente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-210">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="4fe85-211">Implementieren Sie zunächst <xref:System.IDisposable>, und fügen Sie die Factory dann wie in `Pages/EditContact.razor` gezeigt ein:</span><span class="sxs-lookup"><span data-stu-id="4fe85-211">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="4fe85-212">Die Beispiel-App stellt sicher, dass der Kontakt verworfen wird, wenn die Komponente verworfen wird:</span><span class="sxs-lookup"><span data-stu-id="4fe85-212">The sample app ensures the contact is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="4fe85-213">Schließlich wird [`OnInitializedAsync`](xref:blazor/components/lifecycle) überschrieben, um einen neuen Kontext zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-213">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="4fe85-214">In der Beispiel-App lädt [`OnInitializedAsync`](xref:blazor/components/lifecycle) den Kontakt in derselben Methode:</span><span class="sxs-lookup"><span data-stu-id="4fe85-214">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="4fe85-215">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4fe85-215">In the preceding example:</span></span>

* <span data-ttu-id="4fe85-216">Wenn `Busy` auf `true` festgelegt ist, können asynchrone Vorgänge beginnen.</span><span class="sxs-lookup"><span data-stu-id="4fe85-216">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="4fe85-217">Wenn `Busy` auf `false` zurückgesetzt wird, sollten asynchrone Vorgänge abgeschlossen sein.</span><span class="sxs-lookup"><span data-stu-id="4fe85-217">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="4fe85-218">Fügen Sie zusätzliche Fehlerbehandlungslogik in einen `catch`-Block ein.</span><span class="sxs-lookup"><span data-stu-id="4fe85-218">Place additional error handling logic in a `catch` block.</span></span>

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4fe85-219">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4fe85-219">Additional resources</span></span>

> [<span data-ttu-id="4fe85-220">EF Core-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="4fe85-220">EF Core documentation</span></span>](/ef/)
