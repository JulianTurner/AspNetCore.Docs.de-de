---
title: 'ASP.NET Core :::no-loc(Blazor Server)::: mit Entity Framework Core (EFCore)'
author: JeremyLikness
description: Leitfaden zum Verwenden von EF Core in :::no-loc(Blazor Server):::-Apps.
monikerRange: '>= aspnetcore-3.1'
ms.author: jeliknes
ms.custom: mvc
ms.date: 08/14/2020
no-loc:
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/blazor-server-ef-core
ms.openlocfilehash: ac84b9d2fac4fe3df48d356eea3ea48fd23bfda4
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113633"
---
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a><span data-ttu-id="f2eb4-103">ASP.NET Core :::no-loc(Blazor Server)::: mit Entity Framework Core (EFCore)</span><span class="sxs-lookup"><span data-stu-id="f2eb4-103">ASP.NET Core :::no-loc(Blazor Server)::: with Entity Framework Core (EFCore)</span></span>

<span data-ttu-id="f2eb4-104">Von: [Jeremy Likness](https://github.com/JeremyLikness)</span><span class="sxs-lookup"><span data-stu-id="f2eb4-104">By: [Jeremy Likness](https://github.com/JeremyLikness)</span></span>

:::moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f2eb4-105">:::no-loc(Blazor Server)::: ist ein zustandsbehaftetes App-Framework.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-105">:::no-loc(Blazor Server)::: is a stateful app framework.</span></span> <span data-ttu-id="f2eb4-106">Die App unterhält eine kontinuierliche Verbindung mit dem Server, und der Zustand des Benutzers wird im Arbeitsspeicher des Servers in einer *Verbindung* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-106">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit* .</span></span> <span data-ttu-id="f2eb4-107">Ein Beispiel für den Benutzerzustand sind Daten, die in Dienstinstanzen mit [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection) gespeichert sind, die auf die Verbindung beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-107">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="f2eb4-108">Das eindeutige Anwendungsmodell, das :::no-loc(Blazor Server)::: bereitstellt, erfordert eine besondere Vorgehensweise bei der Verwendung von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-108">The unique application model that :::no-loc(Blazor Server)::: provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="f2eb4-109">In diesem Artikel wird EF Core in :::no-loc(Blazor Server):::-Apps behandelt.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-109">This article addresses EF Core in :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="f2eb4-110">:::no-loc(Blazor WebAssembly):::-Apps werden in einer WebAssembly-Sandbox ausgeführt, die die meisten direkten Datenbankverbindungen verhindert.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-110">:::no-loc(Blazor WebAssembly)::: apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="f2eb4-111">Die Ausführung von EF Core in :::no-loc(Blazor WebAssembly)::: sprengt jedoch den Rahmen dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-111">Running EF Core in :::no-loc(Blazor WebAssembly)::: is beyond the scope of this article.</span></span>

<h2 id="sample-app-5x"><span data-ttu-id="f2eb4-112">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="f2eb4-112">Sample app</span></span></h2>

<span data-ttu-id="f2eb4-113">Die Beispiel-App wurde als Referenz für :::no-loc(Blazor Server):::-Apps erstellt, die EF Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-113">The sample app was built as a reference for :::no-loc(Blazor Server)::: apps that use EF Core.</span></span> <span data-ttu-id="f2eb4-114">Die Beispiel-App enthält ein Raster mit Sortier- und Filter-, Lösch-, Hinzufügungs- und Aktualisierungsvorgängen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-114">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="f2eb4-115">Das Beispiel veranschaulicht die Verwendung von EF Core zum Verarbeiten vollständiger Parallelität.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-115">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="f2eb4-116">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f2eb4-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f2eb4-117">Das Beispiel verwendet eine lokale [SQLite](https://www.sqlite.org/index.html)-Datenbank, sodass es auf jeder Plattform verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-117">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="f2eb4-118">Das Beispiel konfiguriert auch die Datenbankprotokollierung, um die generierten SQL-Abfragen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-118">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="f2eb4-119">Dies wird in `appsettings.Development.json` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-119">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="f2eb4-120">Die Raster-, Hinzufügungs- und Anzeigekomponenten verwenden das Muster „Kontext pro Vorgang“, bei dem ein Kontext für jeden Vorgang erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-120">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="f2eb4-121">Die Bearbeitungskomponente verwendet das Muster „Kontext pro Komponente“, bei dem ein Kontext für jede Komponente erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-121">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="f2eb4-122">Einige der Codebeispiele in diesem Thema erfordern Namespaces und Dienste, die nicht gezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-122">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="f2eb4-123">Um den voll funktionsfähigen Code einschließlich der erforderlichen [`@using`](xref:mvc/views/razor#using)- und [`@inject`](xref:mvc/views/razor#inject)-Anweisungen für :::no-loc(Razor):::-Beispiele zu untersuchen, ziehen Sie die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample) zu Rate.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-123">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for :::no-loc(Razor)::: examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample).</span></span>

<h2 id="database-access-5x"><span data-ttu-id="f2eb4-124">Datenbankzugriff</span><span class="sxs-lookup"><span data-stu-id="f2eb4-124">Database access</span></span></h2>

<span data-ttu-id="f2eb4-125">EF Core stützt sich auf einen <xref:Microsoft.EntityFrameworkCore.DbContext> als Mittel zur [Konfiguration des Datenbankzugriffs](/ef/core/miscellaneous/configuring-dbcontext) und um als [*Arbeitseinheit*](https://martinfowler.com/eaaCatalog/unitOfWork.html) zu fungieren.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-125">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="f2eb4-126">EF Core stellt die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterung für ASP.NET Core-Apps bereit, die den Kontext standardmäßig als *bereichsbezogenen* Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-126">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="f2eb4-127">In :::no-loc(Blazor Server):::-Apps können bereichsbezogene Dienstregistrierungen problematisch sein, da die Instanz innerhalb der Verbindung des Benutzers von allen Komponenten gemeinsam genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-127">In :::no-loc(Blazor Server)::: apps, scoped service registrations can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="f2eb4-128"><xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und nicht für gleichzeitige Verwendung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-128"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="f2eb4-129">Die vorhandenen Lebensdauern sind aus den folgenden Gründen ungeeignet:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-129">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="f2eb4-130">**Singleton** verwendet den Zustand für alle Benutzer der App und führt zu einer ungeeigneten gleichzeitigen Verwendung.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-130">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="f2eb4-131">**Scoped (Bereichsbezogen)** (die Standardeinstellung) stellt ein ähnliches Problem zwischen Komponenten für denselben Benutzer dar.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-131">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="f2eb4-132">**Transient (Vorübergehend)** führt zu einer neuen Instanz pro Anforderung. Da Komponenten jedoch langlebig sein können, führt dies zu einem längerlebigen Kontext als möglicherweise beabsichtigt.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-132">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="f2eb4-133">Die folgenden Empfehlungen sollen einen konsistenten Ansatz für die Verwendung von EF Core in :::no-loc(Blazor Server):::-Apps bieten.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-133">The following recommendations are designed to provide a consistent approach to using EF Core in :::no-loc(Blazor Server)::: apps.</span></span>

* <span data-ttu-id="f2eb4-134">Erwägen Sie standardmäßig die Verwendung eines Kontexts pro Vorgang.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-134">By default, consider using one context per operation.</span></span> <span data-ttu-id="f2eb4-135">Der Kontext ist für schnelle Instanziierung mit geringem Mehraufwand konzipiert:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-135">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="f2eb4-136">Verwenden Sie ein Flag, um mehrere gleichzeitige Vorgänge zu verhindern:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-136">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="f2eb4-137">Platzieren Sie Vorgänge nach der Zeile `Loading = true;` im `try`-Block.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-137">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="f2eb4-138">Bei langlebigen Vorgängen, die die Vorteile der [Änderungsnachverfolgung](/ef/core/querying/tracking) oder [Parallelitätssteuerung](/ef/core/saving/concurrency) von EF Core nutzen, [passen Sie den Kontext an die Lebensdauer der Komponente an](#scope-to-the-component-lifetime-5x).</span><span class="sxs-lookup"><span data-stu-id="f2eb4-138">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-5x).</span></span>

<h3 id="new-dbcontext-instances-5x"><span data-ttu-id="f2eb4-139">Neue DbContext-Instanzen</span><span class="sxs-lookup"><span data-stu-id="f2eb4-139">New DbContext instances</span></span></h3>

<span data-ttu-id="f2eb4-140">Die schnellste Möglichkeit zum Erstellen einer neuen <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz ist die Verwendung von `new`, um eine neue Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-140">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="f2eb4-141">Es gibt jedoch mehrere Szenarien, in denen möglicherweise zusätzliche Abhängigkeiten aufgelöst werden müssen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-141">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="f2eb4-142">Beispielsweise möchten Sie möglicherweise [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) verwenden, um den Kontext zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-142">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="f2eb4-143">Die empfohlene Lösung zum Erstellen eines neuen <xref:Microsoft.EntityFrameworkCore.DbContext> mit Abhängigkeiten ist die Verwendung einer Factory.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-143">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="f2eb4-144">EF Core 5.0 oder höher bietet eine integrierte Factory zum Erstellen neuer Kontexte.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-144">EF Core 5.0 or later provides a built-in factory for creating new contexts.</span></span>

<span data-ttu-id="f2eb4-145">Im folgenden Beispiel wird [SQLite](https://www.sqlite.org/index.html) konfiguriert und Datenprotokollierung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-145">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="f2eb4-146">Der Code verwendet eine [Erweiterungsmethode (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/FactoryExtensions.cs) zum Konfigurieren der Datenbankfactory für DI und bietet Standardoptionen:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-146">The code uses an [extension method (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/FactoryExtensions.cs) to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="f2eb4-147">Die Factory wird in Komponenten eingefügt und zum Erstellen neuer Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-147">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="f2eb4-148">Gehen Sie beispielsweise in `Pages/Index.razor` folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-148">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="f2eb4-149">`Wrapper` ist eine [Komponentenreferenz](xref:blazor/components/index#capture-references-to-components) für die `GridWrapper`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-149">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="f2eb4-150">Sehen Sie sich dazu die `Index`-Komponente (`Pages/Index.razor`) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor) an.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-150">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="f2eb4-151">Neue <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanzen können mit einer Factory erstellt werden, mit der Sie die Verbindungszeichenfolge pro `DbContext` konfigurieren können, wenn Sie z. B. das [:::no-loc(Identity):::-Modell von ASP.NET Core](xref:security/authentication/customize_identity_model) verwenden:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-151">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's :::no-loc(Identity)::: model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-5x"><span data-ttu-id="f2eb4-152">Gültigkeitsbereich für die Komponentenlebensdauer</span><span class="sxs-lookup"><span data-stu-id="f2eb4-152">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="f2eb4-153">Möglicherweise möchten Sie einen <xref:Microsoft.EntityFrameworkCore.DbContext> erstellen, der für die Lebensdauer einer Komponente vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-153">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="f2eb4-154">Dies ermöglicht Ihnen die Verwendung als [Arbeitseinheit](https://martinfowler.com/eaaCatalog/unitOfWork.html) und nutzt integrierte Features, z. B. Änderungsnachverfolgung und Parallelitätsauflösung.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-154">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="f2eb4-155">Sie können die Factory verwenden, um einen Kontext zu erstellen und diesen für die gesamte Lebensdauer der Komponente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-155">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="f2eb4-156">Implementieren Sie zunächst <xref:System.IDisposable>, und fügen Sie die Factory dann wie in `Pages/EditContact.razor` gezeigt ein:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-156">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="f2eb4-157">Die Beispiel-App stellt sicher, dass der Kontext verworfen wird, wenn die Komponente verworfen wird:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-157">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="f2eb4-158">Schließlich wird [`OnInitializedAsync`](xref:blazor/components/lifecycle) überschrieben, um einen neuen Kontext zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-158">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="f2eb4-159">In der Beispiel-App lädt [`OnInitializedAsync`](xref:blazor/components/lifecycle) den Kontakt in derselben Methode:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-159">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/5.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="f2eb4-160">Aktivieren der Protokollierung von vertraulicher Daten</span><span class="sxs-lookup"><span data-stu-id="f2eb4-160">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="f2eb4-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> schließt Anwendungsdaten in Ausnahmemeldungen und Frameworkprotokolle ein.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-161"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="f2eb4-162">Die protokollierten Daten können die den Eigenschaften von Entitätsinstanzen zugewiesenen Werte enthalten sowie Parameterwerte für Befehle, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-162">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="f2eb4-163">Das Protokollieren von Daten mit <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> stellt ein **Sicherheitsrisiko** dar, da damit möglicherweise Kennwörter und andere personenbezogene Informationen (PII) verfügbar gemacht werden, wenn SQL-Anweisungen für die Datenbank protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-163">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk** , as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="f2eb4-164">Es wird empfohlen, <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> ausschließlich für Entwicklung und Tests zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-164">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

:::moniker range="< aspnetcore-5.0"

<span data-ttu-id="f2eb4-165">:::no-loc(Blazor Server)::: ist ein zustandsbehaftetes App-Framework.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-165">:::no-loc(Blazor Server)::: is a stateful app framework.</span></span> <span data-ttu-id="f2eb4-166">Die App unterhält eine kontinuierliche Verbindung mit dem Server, und der Zustand des Benutzers wird im Arbeitsspeicher des Servers in einer *Verbindung* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-166">The app maintains an ongoing connection to the server, and the user's state is held in the server's memory in a *circuit* .</span></span> <span data-ttu-id="f2eb4-167">Ein Beispiel für den Benutzerzustand sind Daten, die in Dienstinstanzen mit [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection) gespeichert sind, die auf die Verbindung beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-167">One example of user state is data held in [dependency injection (DI)](xref:fundamentals/dependency-injection) service instances that are scoped to the circuit.</span></span> <span data-ttu-id="f2eb4-168">Das eindeutige Anwendungsmodell, das :::no-loc(Blazor Server)::: bereitstellt, erfordert eine besondere Vorgehensweise bei der Verwendung von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-168">The unique application model that :::no-loc(Blazor Server)::: provides requires a special approach to use Entity Framework Core.</span></span>

> [!NOTE]
> <span data-ttu-id="f2eb4-169">In diesem Artikel wird EF Core in :::no-loc(Blazor Server):::-Apps behandelt.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-169">This article addresses EF Core in :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="f2eb4-170">:::no-loc(Blazor WebAssembly):::-Apps werden in einer WebAssembly-Sandbox ausgeführt, die die meisten direkten Datenbankverbindungen verhindert.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-170">:::no-loc(Blazor WebAssembly)::: apps run in a WebAssembly sandbox that prevents most direct database connections.</span></span> <span data-ttu-id="f2eb4-171">Die Ausführung von EF Core in :::no-loc(Blazor WebAssembly)::: sprengt jedoch den Rahmen dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-171">Running EF Core in :::no-loc(Blazor WebAssembly)::: is beyond the scope of this article.</span></span>

<h2 id="sample-app-3x"><span data-ttu-id="f2eb4-172">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="f2eb4-172">Sample app</span></span></h2>

<span data-ttu-id="f2eb4-173">Die Beispiel-App wurde als Referenz für :::no-loc(Blazor Server):::-Apps erstellt, die EF Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-173">The sample app was built as a reference for :::no-loc(Blazor Server)::: apps that use EF Core.</span></span> <span data-ttu-id="f2eb4-174">Die Beispiel-App enthält ein Raster mit Sortier- und Filter-, Lösch-, Hinzufügungs- und Aktualisierungsvorgängen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-174">The sample app includes a grid with sorting and filtering, delete, add, and update operations.</span></span> <span data-ttu-id="f2eb4-175">Das Beispiel veranschaulicht die Verwendung von EF Core zum Verarbeiten vollständiger Parallelität.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-175">The sample demonstrates use of EF Core to handle optimistic concurrency.</span></span>

<span data-ttu-id="f2eb4-176">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f2eb4-176">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f2eb4-177">Das Beispiel verwendet eine lokale [SQLite](https://www.sqlite.org/index.html)-Datenbank, sodass es auf jeder Plattform verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-177">The sample uses a local [SQLite](https://www.sqlite.org/index.html) database so that it can be used on any platform.</span></span> <span data-ttu-id="f2eb4-178">Das Beispiel konfiguriert auch die Datenbankprotokollierung, um die generierten SQL-Abfragen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-178">The sample also configures database logging to show the SQL queries that are generated.</span></span> <span data-ttu-id="f2eb4-179">Dies wird in `appsettings.Development.json` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-179">This is configured in `appsettings.Development.json`:</span></span>

[!code-json[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/appsettings.Development.json?highlight=8)]

<span data-ttu-id="f2eb4-180">Die Raster-, Hinzufügungs- und Anzeigekomponenten verwenden das Muster „Kontext pro Vorgang“, bei dem ein Kontext für jeden Vorgang erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-180">The grid, add, and view components use the "context-per-operation" pattern, where a context is created for each operation.</span></span> <span data-ttu-id="f2eb4-181">Die Bearbeitungskomponente verwendet das Muster „Kontext pro Komponente“, bei dem ein Kontext für jede Komponente erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-181">The edit component uses the "context-per-component" pattern, where a context is created for each component.</span></span>

> [!NOTE]
> <span data-ttu-id="f2eb4-182">Einige der Codebeispiele in diesem Thema erfordern Namespaces und Dienste, die nicht gezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-182">Some of the code examples in this topic require namespaces and services that aren't shown.</span></span> <span data-ttu-id="f2eb4-183">Um den voll funktionsfähigen Code einschließlich der erforderlichen [`@using`](xref:mvc/views/razor#using)- und [`@inject`](xref:mvc/views/razor#inject)-Anweisungen für :::no-loc(Razor):::-Beispiele zu untersuchen, ziehen Sie die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample) zu Rate.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-183">To inspect the fully working code, including the required [`@using`](xref:mvc/views/razor#using) and [`@inject`](xref:mvc/views/razor#inject) directives for :::no-loc(Razor)::: examples, see the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample).</span></span>

<h2 id="database-access-3x"><span data-ttu-id="f2eb4-184">Datenbankzugriff</span><span class="sxs-lookup"><span data-stu-id="f2eb4-184">Database access</span></span></h2>

<span data-ttu-id="f2eb4-185">EF Core stützt sich auf einen <xref:Microsoft.EntityFrameworkCore.DbContext> als Mittel zur [Konfiguration des Datenbankzugriffs](/ef/core/miscellaneous/configuring-dbcontext) und um als [*Arbeitseinheit*](https://martinfowler.com/eaaCatalog/unitOfWork.html) zu fungieren.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-185">EF Core relies on a <xref:Microsoft.EntityFrameworkCore.DbContext> as the means to [configure database access](/ef/core/miscellaneous/configuring-dbcontext) and act as a [*unit of work*](https://martinfowler.com/eaaCatalog/unitOfWork.html).</span></span> <span data-ttu-id="f2eb4-186">EF Core stellt die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterung für ASP.NET Core-Apps bereit, die den Kontext standardmäßig als *bereichsbezogenen* Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-186">EF Core provides the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension for ASP.NET Core apps that registers the context as a *scoped* service by default.</span></span> <span data-ttu-id="f2eb4-187">In :::no-loc(Blazor Server):::-Apps kann dies problematisch sein, da die Instanz innerhalb der Verbindung des Benutzers von allen Komponenten gemeinsam genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-187">In :::no-loc(Blazor Server)::: apps, this can be problematic because the instance is shared across components within the user's circuit.</span></span> <span data-ttu-id="f2eb4-188"><xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und nicht für gleichzeitige Verwendung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-188"><xref:Microsoft.EntityFrameworkCore.DbContext> isn't thread safe and isn't designed for concurrent use.</span></span> <span data-ttu-id="f2eb4-189">Die vorhandenen Lebensdauern sind aus den folgenden Gründen ungeeignet:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-189">The existing lifetimes are inappropriate for these reasons:</span></span>

* <span data-ttu-id="f2eb4-190">**Singleton** verwendet den Zustand für alle Benutzer der App und führt zu einer ungeeigneten gleichzeitigen Verwendung.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-190">**Singleton** shares state across all users of the app and leads to inappropriate concurrent use.</span></span>
* <span data-ttu-id="f2eb4-191">**Scoped (Bereichsbezogen)** (die Standardeinstellung) stellt ein ähnliches Problem zwischen Komponenten für denselben Benutzer dar.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-191">**Scoped** (the default) poses a similar issue between components for the same user.</span></span>
* <span data-ttu-id="f2eb4-192">**Transient (Vorübergehend)** führt zu einer neuen Instanz pro Anforderung. Da Komponenten jedoch langlebig sein können, führt dies zu einem längerlebigen Kontext als möglicherweise beabsichtigt.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-192">**Transient** results in a new instance per request; but as components can be long-lived, this results in a longer-lived context than may be intended.</span></span>

<span data-ttu-id="f2eb4-193">Die folgenden Empfehlungen sollen einen konsistenten Ansatz für die Verwendung von EF Core in :::no-loc(Blazor Server):::-Apps bieten.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-193">The following recommendations are designed to provide a consistent approach to using EF Core in :::no-loc(Blazor Server)::: apps.</span></span>

* <span data-ttu-id="f2eb4-194">Erwägen Sie standardmäßig die Verwendung eines Kontexts pro Vorgang.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-194">By default, consider using one context per operation.</span></span> <span data-ttu-id="f2eb4-195">Der Kontext ist für schnelle Instanziierung mit geringem Mehraufwand konzipiert:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-195">The context is designed for fast, low overhead instantiation:</span></span>

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* <span data-ttu-id="f2eb4-196">Verwenden Sie ein Flag, um mehrere gleichzeitige Vorgänge zu verhindern:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-196">Use a flag to prevent multiple concurrent operations:</span></span>

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

  <span data-ttu-id="f2eb4-197">Platzieren Sie Vorgänge nach der Zeile `Loading = true;` im `try`-Block.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-197">Place operations after the `Loading = true;` line in the `try` block.</span></span>

* <span data-ttu-id="f2eb4-198">Bei langlebigen Vorgängen, die die Vorteile der [Änderungsnachverfolgung](/ef/core/querying/tracking) oder [Parallelitätssteuerung](/ef/core/saving/concurrency) von EF Core nutzen, [passen Sie den Kontext an die Lebensdauer der Komponente an](#scope-to-the-component-lifetime-3x).</span><span class="sxs-lookup"><span data-stu-id="f2eb4-198">For longer-lived operations that take advantage of EF Core's [change tracking](/ef/core/querying/tracking) or [concurrency control](/ef/core/saving/concurrency), [scope the context to the lifetime of the component](#scope-to-the-component-lifetime-3x).</span></span>

<h3 id="new-dbcontext-instances-3x"><span data-ttu-id="f2eb4-199">Neue DbContext-Instanzen</span><span class="sxs-lookup"><span data-stu-id="f2eb4-199">New DbContext instances</span></span></h3>

<span data-ttu-id="f2eb4-200">Die schnellste Möglichkeit zum Erstellen einer neuen <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz ist die Verwendung von `new`, um eine neue Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-200">The fastest way to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> instance is by using `new` to create a new instance.</span></span> <span data-ttu-id="f2eb4-201">Es gibt jedoch mehrere Szenarien, in denen möglicherweise zusätzliche Abhängigkeiten aufgelöst werden müssen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-201">However, there are several scenarios that may require resolving additional dependencies.</span></span> <span data-ttu-id="f2eb4-202">Beispielsweise möchten Sie möglicherweise [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) verwenden, um den Kontext zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-202">For example, you may wish to use [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) to configure the context.</span></span>

<span data-ttu-id="f2eb4-203">Die empfohlene Lösung zum Erstellen eines neuen <xref:Microsoft.EntityFrameworkCore.DbContext> mit Abhängigkeiten ist die Verwendung einer Factory.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-203">The recommended solution to create a new <xref:Microsoft.EntityFrameworkCore.DbContext> with dependencies is to use a factory.</span></span> <span data-ttu-id="f2eb4-204">Die Beispiel-App implementiert ihre eigene Factory in `Data/DbContextFactory.cs`.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-204">The sample app implements its own factory in `Data/DbContextFactory.cs`.</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/DbContextFactory.cs)]

<span data-ttu-id="f2eb4-205">In der vorhergehenden Factory gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-205">In the preceding factory:</span></span>

* <span data-ttu-id="f2eb4-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> erfüllt alle Abhängigkeiten über den Dienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-206"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> satisfies any dependencies via the service provider.</span></span>
* <span data-ttu-id="f2eb4-207">`IDbContextFactory` ist in EF Core ASP.NET Core 5.0 oder höher verfügbar, sodass die Schnittstelle [in der Beispiel-App für ASP.NET Core 3.x implementiert](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/IDbContextFactory.cs) ist.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-207">`IDbContextFactory` is available in EF Core ASP.NET Core 5.0 or later, so the interface is [implemented in the sample app for ASP.NET Core 3.x](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Data/IDbContextFactory.cs).</span></span>

<span data-ttu-id="f2eb4-208">Im folgenden Beispiel wird [SQLite](https://www.sqlite.org/index.html) konfiguriert und Datenprotokollierung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-208">The following example configures [SQLite](https://www.sqlite.org/index.html) and enables data logging.</span></span> <span data-ttu-id="f2eb4-209">Der Code verwendet eine Erweiterungsmethode zum Konfigurieren der Datenbankfactory für DI und bietet Standardoptionen:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-209">The code uses an extension method to configure the database factory for DI and provide default options:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Startup.cs?name=snippet1)]

<span data-ttu-id="f2eb4-210">Die Factory wird in Komponenten eingefügt und zum Erstellen neuer Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-210">The factory is injected into components and used to create new instances.</span></span> <span data-ttu-id="f2eb4-211">Gehen Sie beispielsweise in `Pages/Index.razor` folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-211">For example, in `Pages/Index.razor`:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> <span data-ttu-id="f2eb4-212">`Wrapper` ist eine [Komponentenreferenz](xref:blazor/components/index#capture-references-to-components) für die `GridWrapper`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-212">`Wrapper` is a [component reference](xref:blazor/components/index#capture-references-to-components) to the `GridWrapper` component.</span></span> <span data-ttu-id="f2eb4-213">Sehen Sie sich dazu die `Index`-Komponente (`Pages/Index.razor`) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor) an.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-213">See the `Index` component (`Pages/Index.razor`) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/Index.razor).</span></span>

<span data-ttu-id="f2eb4-214">Neue <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanzen können mit einer Factory erstellt werden, mit der Sie die Verbindungszeichenfolge pro `DbContext` konfigurieren können, wenn Sie z. B. das [:::no-loc(Identity):::-Modell von ASP.NET Core](xref:security/authentication/customize_identity_model) verwenden:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-214">New <xref:Microsoft.EntityFrameworkCore.DbContext> instances can be created with a factory that allows you to configure the connection string per `DbContext`, such as when you use [ASP.NET Core's :::no-loc(Identity)::: model])(xref:security/authentication/customize_identity_model):</span></span>

```csharp
services.AddDbContextFactory<ApplicationDbContext>(options =>
{
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
});

services.AddScoped<ApplicationDbContext>(p => 
    p.GetRequiredService<IDbContextFactory<ApplicationDbContext>>()
    .CreateDbContext());
```

<h3 id="scope-to-the-component-lifetime-3x"><span data-ttu-id="f2eb4-215">Gültigkeitsbereich für die Komponentenlebensdauer</span><span class="sxs-lookup"><span data-stu-id="f2eb4-215">Scope to the component lifetime</span></span></h3>

<span data-ttu-id="f2eb4-216">Möglicherweise möchten Sie einen <xref:Microsoft.EntityFrameworkCore.DbContext> erstellen, der für die Lebensdauer einer Komponente vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-216">You may wish to create a <xref:Microsoft.EntityFrameworkCore.DbContext> that exists for the lifetime of a component.</span></span> <span data-ttu-id="f2eb4-217">Dies ermöglicht Ihnen die Verwendung als [Arbeitseinheit](https://martinfowler.com/eaaCatalog/unitOfWork.html) und nutzt integrierte Features, z. B. Änderungsnachverfolgung und Parallelitätsauflösung.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-217">This allows you to use it as a [unit of work](https://martinfowler.com/eaaCatalog/unitOfWork.html) and take advantage of built-in features, such as change tracking and concurrency resolution.</span></span>
<span data-ttu-id="f2eb4-218">Sie können die Factory verwenden, um einen Kontext zu erstellen und diesen für die gesamte Lebensdauer der Komponente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-218">You can use the factory to create a context and track it for the lifetime of the component.</span></span> <span data-ttu-id="f2eb4-219">Implementieren Sie zunächst <xref:System.IDisposable>, und fügen Sie die Factory dann wie in `Pages/EditContact.razor` gezeigt ein:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-219">First, implement <xref:System.IDisposable> and inject the factory as shown in `Pages/EditContact.razor`:</span></span>

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

<span data-ttu-id="f2eb4-220">Die Beispiel-App stellt sicher, dass der Kontext verworfen wird, wenn die Komponente verworfen wird:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-220">The sample app ensures the context is disposed when the component is disposed:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

<span data-ttu-id="f2eb4-221">Schließlich wird [`OnInitializedAsync`](xref:blazor/components/lifecycle) überschrieben, um einen neuen Kontext zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-221">Finally, [`OnInitializedAsync`](xref:blazor/components/lifecycle) is overridden to create a new context.</span></span> <span data-ttu-id="f2eb4-222">In der Beispiel-App lädt [`OnInitializedAsync`](xref:blazor/components/lifecycle) den Kontakt in derselben Methode:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-222">In the sample app, [`OnInitializedAsync`](xref:blazor/components/lifecycle) loads the contact in the same method:</span></span>

[!code-csharp[](./common/samples/3.x/:::no-loc(Blazor):::ServerEFCoreSample/:::no-loc(Blazor):::ServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

<span data-ttu-id="f2eb4-223">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-223">In the preceding example:</span></span>

* <span data-ttu-id="f2eb4-224">Wenn `Busy` auf `true` festgelegt ist, können asynchrone Vorgänge beginnen.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-224">When `Busy` is set to `true`, asynchronous operations may begin.</span></span> <span data-ttu-id="f2eb4-225">Wenn `Busy` auf `false` zurückgesetzt wird, sollten asynchrone Vorgänge abgeschlossen sein.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-225">When `Busy` is set back to `false`, asynchronous operations should be finished.</span></span>
* <span data-ttu-id="f2eb4-226">Fügen Sie zusätzliche Fehlerbehandlungslogik in einen `catch`-Block ein.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-226">Place additional error handling logic in a `catch` block.</span></span>

<h3 id="enable-sensitive-data-logging"><span data-ttu-id="f2eb4-227">Aktivieren der Protokollierung von vertraulicher Daten</span><span class="sxs-lookup"><span data-stu-id="f2eb4-227">Enable sensitive data logging</span></span></h3>

<span data-ttu-id="f2eb4-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> schließt Anwendungsdaten in Ausnahmemeldungen und Frameworkprotokolle ein.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-228"><xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> includes application data in exception messages and framework logging.</span></span> <span data-ttu-id="f2eb4-229">Die protokollierten Daten können die den Eigenschaften von Entitätsinstanzen zugewiesenen Werte enthalten sowie Parameterwerte für Befehle, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-229">The logged data can include the values assigned to properties of entity instances and parameter values for commands sent to the database.</span></span> <span data-ttu-id="f2eb4-230">Das Protokollieren von Daten mit <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> stellt ein **Sicherheitsrisiko** dar, da damit möglicherweise Kennwörter und andere personenbezogene Informationen (PII) verfügbar gemacht werden, wenn SQL-Anweisungen für die Datenbank protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="f2eb4-230">Logging data with <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> is a **security risk** , as it may expose passwords and other personally identifiable information (PII) when it logs SQL statements executed against the database.</span></span>

<span data-ttu-id="f2eb4-231">Es wird empfohlen, <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> ausschließlich für Entwicklung und Tests zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="f2eb4-231">We recommend only enabling <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.EnableSensitiveDataLogging%2A> for development and testing:</span></span>

```csharp
#if DEBUG
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db")
        .EnableSensitiveDataLogging());
#else
    services.AddDbContextFactory<ContactContext>(opt =>
        opt.UseSqlite($"Data Source={nameof(ContactContext.ContactsDb)}.db"));
#endif
```

:::moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f2eb4-232">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f2eb4-232">Additional resources</span></span>

* [<span data-ttu-id="f2eb4-233">EF Core-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="f2eb4-233">EF Core documentation</span></span>](/ef/)
