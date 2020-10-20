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
# <a name="aspnet-core-no-locblazor-server-with-entity-framework-core-efcore"></a>ASP.NET Core Blazor Server mit Entity Framework Core (EFCore)

Von: [Jeremy Likness](https://github.com/JeremyLikness)

:::moniker range=">= aspnetcore-5.0"

Blazor Server ist ein zustandsbehaftetes App-Framework. Die App unterhält eine kontinuierliche Verbindung mit dem Server, und der Zustand des Benutzers wird im Arbeitsspeicher des Servers in einer *Verbindung* gespeichert. Ein Beispiel für den Benutzerzustand sind Daten, die in Dienstinstanzen mit [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection) gespeichert sind, die auf die Verbindung beschränkt sind. Das eindeutige Anwendungsmodell, das Blazor Server bereitstellt, erfordert eine besondere Vorgehensweise bei der Verwendung von Entity Framework Core.

> [!NOTE]
> In diesem Artikel wird EF Core in Blazor Server-Apps behandelt. Blazor WebAssembly-Apps werden in einer WebAssembly-Sandbox ausgeführt, die die meisten direkten Datenbankverbindungen verhindert. Die Ausführung von EF Core in Blazor WebAssembly sprengt jedoch den Rahmen dieses Artikels.

<h2 id="sample-app-5x">Beispiel-App</h2>

Die Beispiel-App wurde als Referenz für Blazor Server-Apps erstellt, die EF Core verwenden. Die Beispiel-App enthält ein Raster mit Sortier- und Filter-, Lösch-, Hinzufügungs- und Aktualisierungsvorgängen. Das Beispiel veranschaulicht die Verwendung von EF Core zum Verarbeiten vollständiger Parallelität.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Beispiel verwendet eine lokale [SQLite](https://www.sqlite.org/index.html)-Datenbank, sodass es auf jeder Plattform verwendet werden kann. Das Beispiel konfiguriert auch die Datenbankprotokollierung, um die generierten SQL-Abfragen anzuzeigen. Dies wird in `appsettings.Development.json` konfiguriert:

[!code-json[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Die Raster-, Hinzufügungs- und Anzeigekomponenten verwenden das Muster „Kontext pro Vorgang“, bei dem ein Kontext für jeden Vorgang erstellt wird. Die Bearbeitungskomponente verwendet das Muster „Kontext pro Komponente“, bei dem ein Kontext für jede Komponente erstellt wird.

> [!NOTE]
> Einige der Codebeispiele in diesem Thema erfordern Namespaces und Dienste, die nicht gezeigt werden. Um den voll funktionsfähigen Code einschließlich der erforderlichen [`@using`](xref:mvc/views/razor#using)- und [`@inject`](xref:mvc/views/razor#inject)-Anweisungen für Razor-Beispiele zu untersuchen, ziehen Sie die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample) zu Rate.

<h2 id="database-access-5x">Datenbankzugriff</h2>

EF Core stützt sich auf einen <xref:Microsoft.EntityFrameworkCore.DbContext> als Mittel zur [Konfiguration des Datenbankzugriffs](/ef/core/miscellaneous/configuring-dbcontext) und um als [*Arbeitseinheit*](https://martinfowler.com/eaaCatalog/unitOfWork.html) zu fungieren. EF Core stellt die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterung für ASP.NET Core-Apps bereit, die den Kontext standardmäßig als *bereichsbezogenen* Dienst registriert. In Blazor Server-Apps können bereichsbezogene Dienstregistrierungen problematisch sein, da die Instanz innerhalb der Verbindung des Benutzers von allen Komponenten gemeinsam genutzt wird. <xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und nicht für gleichzeitige Verwendung vorgesehen. Die vorhandenen Lebensdauern sind aus den folgenden Gründen ungeeignet:

* **Singleton** verwendet den Zustand für alle Benutzer der App und führt zu einer ungeeigneten gleichzeitigen Verwendung.
* **Scoped (Bereichsbezogen)** (die Standardeinstellung) stellt ein ähnliches Problem zwischen Komponenten für denselben Benutzer dar.
* **Transient (Vorübergehend)** führt zu einer neuen Instanz pro Anforderung. Da Komponenten jedoch langlebig sein können, führt dies zu einem längerlebigen Kontext als möglicherweise beabsichtigt.

Die folgenden Empfehlungen sollen einen konsistenten Ansatz für die Verwendung von EF Core in Blazor Server-Apps bieten.

* Erwägen Sie standardmäßig die Verwendung eines Kontexts pro Vorgang. Der Kontext ist für schnelle Instanziierung mit geringem Mehraufwand konzipiert:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Verwenden Sie ein Flag, um mehrere gleichzeitige Vorgänge zu verhindern:

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

  Platzieren Sie Vorgänge nach der Zeile `Loading = true;` im `try`-Block.

* Bei langlebigen Vorgängen, die die Vorteile der [Änderungsnachverfolgung](/ef/core/querying/tracking) oder [Parallelitätssteuerung](/ef/core/saving/concurrency) von EF Core nutzen, [passen Sie den Kontext an die Lebensdauer der Komponente an](#scope-to-the-component-lifetime-5x).

<h3 id="new-dbcontext-instances-5x">Neue DbContext-Instanzen</h3>

Die schnellste Möglichkeit zum Erstellen einer neuen <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz ist die Verwendung von `new`, um eine neue Instanz zu erstellen. Es gibt jedoch mehrere Szenarien, in denen möglicherweise zusätzliche Abhängigkeiten aufgelöst werden müssen. Beispielsweise möchten Sie möglicherweise [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) verwenden, um den Kontext zu konfigurieren.

Die empfohlene Lösung zum Erstellen eines neuen <xref:Microsoft.EntityFrameworkCore.DbContext> mit Abhängigkeiten ist die Verwendung einer Factory. EF Core 5.0 oder höher bietet eine integrierte Factory zum Erstellen neuer Kontexte.

Im folgenden Beispiel wird [SQLite](https://www.sqlite.org/index.html) konfiguriert und Datenprotokollierung aktiviert. Der Code verwendet eine [Erweiterungsmethode (`AddDbContextFactory`)](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/FactoryExtensions.cs) zum Konfigurieren der Datenbankfactory für DI und bietet Standardoptionen:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Die Factory wird in Komponenten eingefügt und zum Erstellen neuer Instanzen verwendet. Gehen Sie beispielsweise in `Pages/Index.razor` folgendermaßen vor:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` ist eine [Komponentenreferenz](xref:blazor/components/index#capture-references-to-components) für die `GridWrapper`-Komponente. Sehen Sie sich dazu die `Index`-Komponente (`Pages/Index.razor`) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor) an.

<h3 id="scope-to-the-component-lifetime-5x">Gültigkeitsbereich für die Komponentenlebensdauer</h3>

Möglicherweise möchten Sie einen <xref:Microsoft.EntityFrameworkCore.DbContext> erstellen, der für die Lebensdauer einer Komponente vorhanden ist. Dies ermöglicht Ihnen die Verwendung als [Arbeitseinheit](https://martinfowler.com/eaaCatalog/unitOfWork.html) und nutzt integrierte Features, z. B. Änderungsnachverfolgung und Parallelitätsauflösung.
Sie können die Factory verwenden, um einen Kontext zu erstellen und diesen für die gesamte Lebensdauer der Komponente nachzuverfolgen. Implementieren Sie zunächst <xref:System.IDisposable>, und fügen Sie die Factory dann wie in `Pages/EditContact.razor` gezeigt ein:

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

Die Beispiel-App stellt sicher, dass der Kontext verworfen wird, wenn die Komponente verworfen wird:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Schließlich wird [`OnInitializedAsync`](xref:blazor/components/lifecycle) überschrieben, um einen neuen Kontext zu erstellen. In der Beispiel-App lädt [`OnInitializedAsync`](xref:blazor/components/lifecycle) den Kontakt in derselben Methode:

[!code-csharp[](./common/samples/5.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

:::moniker-end

:::moniker range="< aspnetcore-5.0"

Blazor Server ist ein zustandsbehaftetes App-Framework. Die App unterhält eine kontinuierliche Verbindung mit dem Server, und der Zustand des Benutzers wird im Arbeitsspeicher des Servers in einer *Verbindung* gespeichert. Ein Beispiel für den Benutzerzustand sind Daten, die in Dienstinstanzen mit [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection) gespeichert sind, die auf die Verbindung beschränkt sind. Das eindeutige Anwendungsmodell, das Blazor Server bereitstellt, erfordert eine besondere Vorgehensweise bei der Verwendung von Entity Framework Core.

> [!NOTE]
> In diesem Artikel wird EF Core in Blazor Server-Apps behandelt. Blazor WebAssembly-Apps werden in einer WebAssembly-Sandbox ausgeführt, die die meisten direkten Datenbankverbindungen verhindert. Die Ausführung von EF Core in Blazor WebAssembly sprengt jedoch den Rahmen dieses Artikels.

<h2 id="sample-app-3x">Beispiel-App</h2>

Die Beispiel-App wurde als Referenz für Blazor Server-Apps erstellt, die EF Core verwenden. Die Beispiel-App enthält ein Raster mit Sortier- und Filter-, Lösch-, Hinzufügungs- und Aktualisierungsvorgängen. Das Beispiel veranschaulicht die Verwendung von EF Core zum Verarbeiten vollständiger Parallelität.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Beispiel verwendet eine lokale [SQLite](https://www.sqlite.org/index.html)-Datenbank, sodass es auf jeder Plattform verwendet werden kann. Das Beispiel konfiguriert auch die Datenbankprotokollierung, um die generierten SQL-Abfragen anzuzeigen. Dies wird in `appsettings.Development.json` konfiguriert:

[!code-json[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/appsettings.Development.json?highlight=8)]

Die Raster-, Hinzufügungs- und Anzeigekomponenten verwenden das Muster „Kontext pro Vorgang“, bei dem ein Kontext für jeden Vorgang erstellt wird. Die Bearbeitungskomponente verwendet das Muster „Kontext pro Komponente“, bei dem ein Kontext für jede Komponente erstellt wird.

> [!NOTE]
> Einige der Codebeispiele in diesem Thema erfordern Namespaces und Dienste, die nicht gezeigt werden. Um den voll funktionsfähigen Code einschließlich der erforderlichen [`@using`](xref:mvc/views/razor#using)- und [`@inject`](xref:mvc/views/razor#inject)-Anweisungen für Razor-Beispiele zu untersuchen, ziehen Sie die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample) zu Rate.

<h2 id="database-access-3x">Datenbankzugriff</h2>

EF Core stützt sich auf einen <xref:Microsoft.EntityFrameworkCore.DbContext> als Mittel zur [Konfiguration des Datenbankzugriffs](/ef/core/miscellaneous/configuring-dbcontext) und um als [*Arbeitseinheit*](https://martinfowler.com/eaaCatalog/unitOfWork.html) zu fungieren. EF Core stellt die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterung für ASP.NET Core-Apps bereit, die den Kontext standardmäßig als *bereichsbezogenen* Dienst registriert. In Blazor Server-Apps kann dies problematisch sein, da die Instanz innerhalb der Verbindung des Benutzers von allen Komponenten gemeinsam genutzt wird. <xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und nicht für gleichzeitige Verwendung vorgesehen. Die vorhandenen Lebensdauern sind aus den folgenden Gründen ungeeignet:

* **Singleton** verwendet den Zustand für alle Benutzer der App und führt zu einer ungeeigneten gleichzeitigen Verwendung.
* **Scoped (Bereichsbezogen)** (die Standardeinstellung) stellt ein ähnliches Problem zwischen Komponenten für denselben Benutzer dar.
* **Transient (Vorübergehend)** führt zu einer neuen Instanz pro Anforderung. Da Komponenten jedoch langlebig sein können, führt dies zu einem längerlebigen Kontext als möglicherweise beabsichtigt.

Die folgenden Empfehlungen sollen einen konsistenten Ansatz für die Verwendung von EF Core in Blazor Server-Apps bieten.

* Erwägen Sie standardmäßig die Verwendung eines Kontexts pro Vorgang. Der Kontext ist für schnelle Instanziierung mit geringem Mehraufwand konzipiert:

  ```csharp
  var using context = new MyContext();

  return await context.MyEntities.ToListAsync();
  ```

* Verwenden Sie ein Flag, um mehrere gleichzeitige Vorgänge zu verhindern:

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

  Platzieren Sie Vorgänge nach der Zeile `Loading = true;` im `try`-Block.

* Bei langlebigen Vorgängen, die die Vorteile der [Änderungsnachverfolgung](/ef/core/querying/tracking) oder [Parallelitätssteuerung](/ef/core/saving/concurrency) von EF Core nutzen, [passen Sie den Kontext an die Lebensdauer der Komponente an](#scope-to-the-component-lifetime-3x).

<h3 id="new-dbcontext-instances-3x">Neue DbContext-Instanzen</h3>

Die schnellste Möglichkeit zum Erstellen einer neuen <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanz ist die Verwendung von `new`, um eine neue Instanz zu erstellen. Es gibt jedoch mehrere Szenarien, in denen möglicherweise zusätzliche Abhängigkeiten aufgelöst werden müssen. Beispielsweise möchten Sie möglicherweise [`DbContextOptions`](/ef/core/miscellaneous/configuring-dbcontext#configuring-dbcontextoptions) verwenden, um den Kontext zu konfigurieren.

Die empfohlene Lösung zum Erstellen eines neuen <xref:Microsoft.EntityFrameworkCore.DbContext> mit Abhängigkeiten ist die Verwendung einer Factory. Die Beispiel-App implementiert ihre eigene Factory in `Data/DbContextFactory.cs`.

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/DbContextFactory.cs)]

In der vorhergehenden Factory gilt Folgendes:

* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> erfüllt alle Abhängigkeiten über den Dienstanbieter.
* `IDbContextFactory` ist in EF Core ASP.NET Core 5.0 oder höher verfügbar, sodass die Schnittstelle [in der Beispiel-App für ASP.NET Core 3.x implementiert](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Data/IDbContextFactory.cs) ist.

Im folgenden Beispiel wird [SQLite](https://www.sqlite.org/index.html) konfiguriert und Datenprotokollierung aktiviert. Der Code verwendet eine Erweiterungsmethode zum Konfigurieren der Datenbankfactory für DI und bietet Standardoptionen:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Startup.cs?name=snippet1)]

Die Factory wird in Komponenten eingefügt und zum Erstellen neuer Instanzen verwendet. Gehen Sie beispielsweise in `Pages/Index.razor` folgendermaßen vor:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor?name=snippet1)]

> [!NOTE]
> `Wrapper` ist eine [Komponentenreferenz](xref:blazor/components/index#capture-references-to-components) für die `GridWrapper`-Komponente. Sehen Sie sich dazu die `Index`-Komponente (`Pages/Index.razor`) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/Index.razor) an.

<h3 id="scope-to-the-component-lifetime-3x">Gültigkeitsbereich für die Komponentenlebensdauer</h3>

Möglicherweise möchten Sie einen <xref:Microsoft.EntityFrameworkCore.DbContext> erstellen, der für die Lebensdauer einer Komponente vorhanden ist. Dies ermöglicht Ihnen die Verwendung als [Arbeitseinheit](https://martinfowler.com/eaaCatalog/unitOfWork.html) und nutzt integrierte Features, z. B. Änderungsnachverfolgung und Parallelitätsauflösung.
Sie können die Factory verwenden, um einen Kontext zu erstellen und diesen für die gesamte Lebensdauer der Komponente nachzuverfolgen. Implementieren Sie zunächst <xref:System.IDisposable>, und fügen Sie die Factory dann wie in `Pages/EditContact.razor` gezeigt ein:

```razor
@implements IDisposable
@inject IDbContextFactory<ContactContext> DbFactory
```

Die Beispiel-App stellt sicher, dass der Kontext verworfen wird, wenn die Komponente verworfen wird:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet1)]

Schließlich wird [`OnInitializedAsync`](xref:blazor/components/lifecycle) überschrieben, um einen neuen Kontext zu erstellen. In der Beispiel-App lädt [`OnInitializedAsync`](xref:blazor/components/lifecycle) den Kontakt in derselben Methode:

[!code-csharp[](./common/samples/3.x/BlazorServerEFCoreSample/BlazorServerDbContextExample/Pages/EditContact.razor?name=snippet2)]

Im vorherigen Beispiel:

* Wenn `Busy` auf `true` festgelegt ist, können asynchrone Vorgänge beginnen. Wenn `Busy` auf `false` zurückgesetzt wird, sollten asynchrone Vorgänge abgeschlossen sein.
* Fügen Sie zusätzliche Fehlerbehandlungslogik in einen `catch`-Block ein.

:::moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [EF Core-Dokumentation](/ef/)
