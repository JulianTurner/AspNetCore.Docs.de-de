---
title: Abhängigkeitsinjektion in ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Blazor-Apps Dienste in Komponenten einfügen können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/dependency-injection
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: af6b645fc3c398414c85c78e1cfeb213e538c2a6
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506798"
---
# <a name="aspnet-core-no-locblazor-dependency-injection"></a>Abhängigkeitsinjektion in ASP.NET Core Blazor

Von [Rainer Stropek](https://www.timecockpit.com) und [Mike Rousos](https://github.com/mjrousos)

Die Abhängigkeitsinjektion, die sogenannte [Dependency Injection](xref:fundamentals/dependency-injection), ist ein Verfahren für den Zugriff auf Dienste, die an einer zentralen Stelle konfiguriert wurde:

* Dienste, die beim Framework registriert wurden, können direkt in Komponenten von Blazor-Apps eingefügt werden.
* Blazor-Apps definieren und registrieren benutzerdefinierte Dienste und machen sie über die Abhängigkeitsinjektion in der gesamten App verfügbar.

## <a name="default-services"></a>Standarddienste

Die in der folgenden Tabelle enthaltenen Dienste werden häufig in Blazor-Apps verwendet.

| Dienst | Lebensdauer | Beschreibung |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Bereichsbezogen | <p>Stellt Methoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten aus einer Ressource bereit, die von einem URI identifiziert wird.</p><p>Die Instanz von <xref:System.Net.Http.HttpClient> in einer Blazor WebAssembly-App verwendet den Browser für die Behandlung des HTTP-Datenverkehrs im Hintergrund.</p><p>Blazor Server-Apps enthalten standardmäßig keinen <xref:System.Net.Http.HttpClient>, der als Dienst konfiguriert ist. Stellen Sie einen <xref:System.Net.Http.HttpClient> für eine Blazor Server-App bereit.</p><p>Weitere Informationen finden Sie unter <xref:blazor/call-web-api>.</p><p><xref:System.Net.Http.HttpClient> wird als bereichsbezogener Dienst, nicht als Singleton registriert: Weitere Informationen finden Sie im Abschnitt zur [Dienstlebensdauer](#service-lifetime).</p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p>**Blazor WebAssembly** : Singleton</p><p>**Blazor Server** : Bereichsbezogen</p> | Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe verteilt werden. Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet>. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p>**Blazor WebAssembly** : Singleton</p><p>**Blazor Server** : Bereichsbezogen</p> | Enthält Hilfsprogramme für die Arbeit mit URIs und dem Navigationszustand. Weitere Informationen finden Sie unter [Hilfsprogramme für URI und Navigationszustand](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers). |

Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit. Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu.

## <a name="add-services-to-an-app"></a>Hinzufügen von Diensten zu einer App

::: zone pivot="webassembly"

Konfigurieren Sie Dienste für die Dienstsammlung der App in der `Program.Main`-Methode von `Program.cs`. Im folgenden Beispiel ist die `MyDependency`-Implementierung für `IMyDependency` registriert:

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

Nachdem der Host erstellt wurde, kann auf die Dienste vom Stammbereich der Abhängigkeitsinjektion aus zugegriffen werden, bevor Komponenten bereitgestellt werden. Dies kann für die Ausführung der Initialisierungslogik vor dem Rendern von Inhalten nützlich sein:

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

Der Host stellt eine zentrale Konfigurationsinstanz für die App zur Verfügung. Ausgehend vom vorhergehenden Beispiel wird die URL des Wetterdiensts von einer Standardkonfigurationsquelle (z. B. `appsettings.json`) an `InitializeWeatherAsync` übergeben:

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

Nachdem Sie eine neue App erstellt haben, sehen Sie sich die `Startup.ConfigureServices`-Methode in `Startup.cs` an:

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

Der Methode <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> wird ein <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>-Objekt übergeben, das eine Liste von [Dienstdeskriptorobjekten](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) ist. Dienste werden in der `ConfigureServices`-Methode durch Bereitstellung von Dienstdeskriptoren in der Dienstsammlung hinzugefügt. Das folgende Beispiel veranschaulicht das Konzept mit der `IDataAccess`-Schnittstelle und seiner konkreten Implementierung `DataAccess`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a>Lebensdauer von Diensten

Die Dienste können mit den in der folgenden Tabelle angegebenen Lebensdauern konfiguriert werden.

| Lebensdauer | Beschreibung |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p>Blazor WebAssembly-Apps verfügen derzeit nicht über ein Konzept für Bereiche von Abhängigkeitsinjektionen. `Scoped`-registrierte Dienste verhalten sich wie `Singleton`-Dienste.</p><p>Das Blazor Server-Hostingmodell unterstützt die `Scoped`-Lebensdauer über HTTP-Anforderungen hinweg, nicht jedoch über SignalR-Verbindungs- bzw. Leitungsmeldungen zwischen Komponenten hinweg, die auf dem Client geladen sind. Beim Navigieren zwischen Seiten oder Ansichten oder von einer Seite oder Ansicht zu einer Komponente verarbeitet der Razor Pages- bzw. MVC-Teil der App bereichsbezogene Dienste normal und erstellt die Dienste für *jede HTTP-Anforderung* neu. Beim Navigieren zwischen Komponenten auf dem Client werden bereichsbezogene Dienste nicht neu erstellt. In diesem Fall erfolgt die Kommunikation mit dem Server über die SignalR-Verbindung der Benutzerleitung, nicht über HTTP-Anforderungen. In den folgenden Komponentenszenarien auf dem Client werden bereichsbezogene Dienste neu erstellt, weil für den Benutzer eine neue Leitung erstellt wird:</p><ul><li>Der Benutzer schließt das Browserfenster. Der Benutzer öffnet ein neues Fenster und navigiert zur App zurück.</li><li>Der Benutzer schließt die letzte Registerkarte der App in einem Browserfenster. Der Benutzer öffnet eine neue Registerkarte und navigiert zur App zurück.</li><li>Der Benutzer wählt die Schaltfläche des Browsers zum erneuten Laden oder Aktualisieren aus.</li></ul><p>Weitere Informationen zum Beibehalten des Benutzerstatus in Blazor Server-Apps über bereichsbezogene Dienste hinweg finden Sie unter <xref:blazor/hosting-models?pivots=server>.</p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Die Abhängigkeitsinjektion erstellt eine *Einzelinstanz* des Diensts. Alle Komponenten, die einen `Singleton`-Dienst erfordern, erhalten eine Instanz desselben Diensts. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Immer wenn eine Komponente eine Instanz eines `Transient`-Diensts aus dem Dienstcontainer erhält, erhält sie eine *neue Instanz* des Diensts. |

Das Abhängigkeitsinjektionssystem basiert auf dem Abhängigkeitsinjektionssystem in ASP.NET Core. Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Anfordern eines Diensts in einer Komponente

Nachdem die Dienste der Dienstsammlung hinzugefügt wurden, fügen Sie die Dienste mit der Razor-Anweisung [\@inject](xref:mvc/views/razor#inject) in die Komponenten ein. [`@inject`](xref:mvc/views/razor#inject) besitzt zwei Parameter:

* Typ: Der Typ des einzufügenden Diensts.
* Eigenschaft: Der Name der Eigenschaft, die den eingefügten App-Dienst erhält. Die Eigenschaft erfordert keine manuelle Erstellung. Der Compiler erstellt die Eigenschaft.

Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.

Verwenden Sie mehrere [`@inject`](xref:mvc/views/razor#inject)-Anweisungen, um verschiedene Dienste einzufügen.

Das folgende Beispiel veranschaulicht die Verwendung von [`@inject`](xref:mvc/views/razor#inject). Der Dienst, der `Services.IDataAccess` implementiert, wird in die Eigenschaft `DataRepository` der Komponente eingefügt. Beachten Sie, dass der Code nur die Abstraktion `IDataAccess` verwendet:

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

Intern verwendet die generierte Eigenschaft (`DataRepository`) das Attribut [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute). In der Regel wird dieses Attribut nicht direkt verwendet. Wenn eine Basisklasse für Komponenten erforderlich ist und die eingefügten Eigenschaften auch für die Basisklasse erforderlich sind, fügen Sie das [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut manuell hinzu:

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

In Komponenten, die von der Basisklasse abgeleitet sind, ist die [`@inject`](xref:mvc/views/razor#inject)-Direktive nicht erforderlich. Das <xref:Microsoft.AspNetCore.Components.InjectAttribute> der Basisklasse ist ausreichend:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Verwenden der Abhängigkeitsinjektion in Diensten

Komplexe Dienste können zusätzliche Dienste erfordern. Im folgenden Beispiel ist für `DataAccess` der <xref:System.Net.Http.HttpClient>-Standarddienst erforderlich. [`@inject`](xref:mvc/views/razor#inject) (oder das [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut) ist nicht für die Verwendung in Diensten verfügbar. Stattdessen muss die *Constructor Injection* verwendet werden. Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt. Wenn die Abhängigkeitsinjektion den Dienst erstellt, erkennt sie die erforderlichen Dienste im Konstruktor und stellt sie entsprechend zur Verfügung. Im folgenden Beispiel empfängt der Konstruktor einen <xref:System.Net.Http.HttpClient> über die Abhängigkeitsinjektion. <xref:System.Net.Http.HttpClient> ist ein Standarddienst.

```csharp
using System.Net.Http;

public class DataAccess : IDataAccess
{
    public DataAccess(HttpClient http)
    {
        ...
    }
}
```

Voraussetzungen für die Constructor Injection:

* Es muss einen Konstruktor geben, dessen Argumente alle von der Abhängigkeitsinjektion erfüllt werden können. Zusätzliche, nicht durch die Abhängigkeitsinjektion abgedeckte Parameter sind zulässig, wenn sie Standardwerte angeben.
* Der anwendbare Konstruktor muss `public` sein.
* Es muss ein anwendbarer Konstruktor vorhanden sein. Im Falle einer Mehrdeutigkeit löst die Abhängigkeitsinjektion eine Ausnahme aus.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Hilfsprogramm-Basiskomponentenklassen zur Verwaltung eines Bereichs für die Abhängigkeitsinjektion

In ASP.NET Core-Apps werden bereichsbezogene Dienste in der Regel der aktuellen Anforderung zugeordnet. Nachdem die Anforderung abgeschlossen ist, werden alle bereichsbezogenen oder vorübergehenden Dienste vom Abhängigkeitsinjektionssystem entsorgt. In Blazor Server-Apps besteht der Anforderungsbereich für die Dauer der Clientverbindung, was dazu führen kann, dass vorübergehende und bereichsbezogene Dienste viel länger als erwartet leben. In Blazor WebAssembly-Apps werden Dienste, die mit einer bereichsbezogenen Lebensdauer registriert sind, als Singletons behandelt, sodass sie länger leben als bereichsbezogene Dienste in typischen ASP.NET Core-Apps.

> [!NOTE]
> Informationen zum Erkennen verwerfbarer vorübergehender Dienste in einer App finden Sie im Abschnitt [Erkennen vorübergehender verwerfbarer Objekte](#detect-transient-disposables).

Ein Ansatz, der die Lebensdauer eines Diensts in Blazor-Apps begrenzt, ist die Verwendung des Typs <xref:Microsoft.AspNetCore.Components.OwningComponentBase>. <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ist ein abstrakter, von <xref:Microsoft.AspNetCore.Components.ComponentBase> abgeleiteter Typ, der einen der Lebensdauer der Komponente entsprechenden Bereich für die Abhängigkeitsinjektion erstellt. Mit diesem Bereich ist es möglich, Abhängigkeitsinjektionsdienste mit einer bereichsbezogenen Lebensdauer zu nutzen und sie so lange wie die Komponente zu nutzen. Wenn die Komponente zerstört wird, werden auch die Dienste des bereichsbezogenen Dienstanbieters der Komponente entsorgt. Dies kann für Dienste nützlich sein, für die Folgendes gilt:

* Sie sollten innerhalb einer Komponente wiederverwendet werden, da die vorübergehende Lebensdauer unangemessen ist.
* Sie sollten nicht komponentenübergreifend freigegeben werden, da die Singleton-Lebensdauer unangemessen ist.

Zwei Versionen des Typs <xref:Microsoft.AspNetCore.Components.OwningComponentBase> sind verfügbar:

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ist ein abstraktes, verwerfbares, untergeordnetes Element vom Typ <xref:Microsoft.AspNetCore.Components.ComponentBase> mit einer geschützten <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>-Eigenschaft vom Typ <xref:System.IServiceProvider>. Dieser Anbieter kann zur Auflösung von Diensten verwendet werden, die der Lebensdauer der Komponente zugeordnet sind.

  Abhängigkeitsinjektionsdienste, die mit [`@inject`](xref:mvc/views/razor#inject) oder dem [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut in die Komponente eingefügt werden, werden nicht im Gültigkeitsbereich der Komponente erstellt. Um den Bereich der Komponente zu verwenden, müssen die Dienste mit <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> oder <xref:System.IServiceProvider.GetService%2A> aufgelöst werden. Allen Diensten, die unter Verwendung des <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>-Anbieters aufgelöst werden, werden ihre Abhängigkeiten aus demselben Bereich bereitgestellt.

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> ist abgeleitet von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> und fügt eine Eigenschaft <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> hinzu, die eine Instanz von `T` vom bereichsbezogenen Abhängigkeitsinjektionsanbieter zurückgibt. Dieser Typ ist eine komfortable Methode für den Zugriff auf bereichsbezogene Dienste, ohne eine Instanz von <xref:System.IServiceProvider> zu verwenden, wenn es einen primären Dienst gibt, den die Anwendung aus dem Abhängigkeitsinjektionscontainer unter Verwendung des Bereichs der Komponente benötigt. Die Eigenschaft <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> ist verfügbar, sodass die App bei Bedarf auch Dienste mit anderen Typen erhalten kann.

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a>Verwenden eines DbContext von Entity Framework Core (EF Core) aus DI

Weitere Informationen finden Sie unter <xref:blazor/blazor-server-ef-core>.

::: moniker range="< aspnetcore-5.0"

## <a name="detect-transient-disposables"></a>Erkennen vorübergehender verwerfbarer Dienste

In den folgenden Beispielen wird veranschaulicht, wie verwerfbare vorübergehende Dienste in einer App erkannt werden, die <xref:Microsoft.AspNetCore.Components.OwningComponentBase> verwenden sollte. Weitere Informationen finden Sie im Abschnitt [Hilfsprogramm-Basiskomponentenklassen zur Verwaltung eines Bereichs für die Abhängigkeitsinjektion](#utility-base-component-classes-to-manage-a-di-scope).

::: zone pivot="webassembly"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

Im folgenden Beispiel wird `TransientDisposable` erkannt (`Program.cs`):

<!-- moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

moniker-end 

moniker range="< aspnetcore-5.0" -->

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

<!-- moniker-end -->

::: zone-end

::: zone pivot="server"

`DetectIncorrectUsagesOfTransientDisposables.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> hinzu:

```csharp
using Microsoft.Extensions.DependencyInjection;
```

In `Program.CreateHostBuilder` von `Program.cs`:

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

Im folgenden Beispiel wird `TransientDependency` erkannt (`Startup.cs`):

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/dependency-injection>
* [`IDisposable`-Anleitung für vorübergehende and freigegebene Instanzen](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
