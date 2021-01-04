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
# <a name="aspnet-core-no-locblazor-dependency-injection"></a><span data-ttu-id="a5f78-103">Abhängigkeitsinjektion in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a5f78-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="a5f78-104">Von [Rainer Stropek](https://www.timecockpit.com) und [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="a5f78-104">By [Rainer Stropek](https://www.timecockpit.com) and [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="a5f78-105">Die Abhängigkeitsinjektion, die sogenannte [Dependency Injection](xref:fundamentals/dependency-injection), ist ein Verfahren für den Zugriff auf Dienste, die an einer zentralen Stelle konfiguriert wurde:</span><span class="sxs-lookup"><span data-stu-id="a5f78-105">[Dependency injection (DI)](xref:fundamentals/dependency-injection) is a technique for accessing services configured in a central location:</span></span>

* <span data-ttu-id="a5f78-106">Dienste, die beim Framework registriert wurden, können direkt in Komponenten von Blazor-Apps eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a5f78-106">Framework-registered services can be injected directly into components of Blazor apps.</span></span>
* <span data-ttu-id="a5f78-107">Blazor-Apps definieren und registrieren benutzerdefinierte Dienste und machen sie über die Abhängigkeitsinjektion in der gesamten App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a5f78-107">Blazor apps define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="default-services"></a><span data-ttu-id="a5f78-108">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="a5f78-108">Default services</span></span>

<span data-ttu-id="a5f78-109">Die in der folgenden Tabelle enthaltenen Dienste werden häufig in Blazor-Apps verwendet.</span><span class="sxs-lookup"><span data-stu-id="a5f78-109">The services shown the following table are commonly used in Blazor apps.</span></span>

| <span data-ttu-id="a5f78-110">Dienst</span><span class="sxs-lookup"><span data-stu-id="a5f78-110">Service</span></span> | <span data-ttu-id="a5f78-111">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="a5f78-111">Lifetime</span></span> | <span data-ttu-id="a5f78-112">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a5f78-112">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="a5f78-113">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="a5f78-113">Scoped</span></span> | <p><span data-ttu-id="a5f78-114">Stellt Methoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten aus einer Ressource bereit, die von einem URI identifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="a5f78-114">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span></p><p><span data-ttu-id="a5f78-115">Die Instanz von <xref:System.Net.Http.HttpClient> in einer Blazor WebAssembly-App verwendet den Browser für die Behandlung des HTTP-Datenverkehrs im Hintergrund.</span><span class="sxs-lookup"><span data-stu-id="a5f78-115">The instance of <xref:System.Net.Http.HttpClient> in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span></p><p><span data-ttu-id="a5f78-116">Blazor Server-Apps enthalten standardmäßig keinen <xref:System.Net.Http.HttpClient>, der als Dienst konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="a5f78-116">Blazor Server apps don't include an <xref:System.Net.Http.HttpClient> configured as a service by default.</span></span> <span data-ttu-id="a5f78-117">Stellen Sie einen <xref:System.Net.Http.HttpClient> für eine Blazor Server-App bereit.</span><span class="sxs-lookup"><span data-stu-id="a5f78-117">Provide an <xref:System.Net.Http.HttpClient> to a Blazor Server app.</span></span></p><p><span data-ttu-id="a5f78-118">Weitere Informationen finden Sie unter <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="a5f78-118">For more information, see <xref:blazor/call-web-api>.</span></span></p><p><span data-ttu-id="a5f78-119"><xref:System.Net.Http.HttpClient> wird als bereichsbezogener Dienst, nicht als Singleton registriert:</span><span class="sxs-lookup"><span data-stu-id="a5f78-119">An <xref:System.Net.Http.HttpClient> is registered as a scoped service, not singleton.</span></span> <span data-ttu-id="a5f78-120">Weitere Informationen finden Sie im Abschnitt zur [Dienstlebensdauer](#service-lifetime).</span><span class="sxs-lookup"><span data-stu-id="a5f78-120">For more information, see the [Service lifetime](#service-lifetime) section.</span></span></p> |
| <xref:Microsoft.JSInterop.IJSRuntime> | <p><span data-ttu-id="a5f78-121">**Blazor WebAssembly** : Singleton</span><span class="sxs-lookup"><span data-stu-id="a5f78-121">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="a5f78-122">**Blazor Server** : Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="a5f78-122">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="a5f78-123">Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe verteilt werden.</span><span class="sxs-lookup"><span data-stu-id="a5f78-123">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="a5f78-124">Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet>.</span><span class="sxs-lookup"><span data-stu-id="a5f78-124">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | <p><span data-ttu-id="a5f78-125">**Blazor WebAssembly** : Singleton</span><span class="sxs-lookup"><span data-stu-id="a5f78-125">**Blazor WebAssembly**: Singleton</span></span></p><p><span data-ttu-id="a5f78-126">**Blazor Server** : Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="a5f78-126">**Blazor Server**: Scoped</span></span></p> | <span data-ttu-id="a5f78-127">Enthält Hilfsprogramme für die Arbeit mit URIs und dem Navigationszustand.</span><span class="sxs-lookup"><span data-stu-id="a5f78-127">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="a5f78-128">Weitere Informationen finden Sie unter [Hilfsprogramme für URI und Navigationszustand](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="a5f78-128">For more information, see [URI and navigation state helpers](xref:blazor/fundamentals/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="a5f78-129">Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit.</span><span class="sxs-lookup"><span data-stu-id="a5f78-129">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="a5f78-130">Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu.</span><span class="sxs-lookup"><span data-stu-id="a5f78-130">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="a5f78-131">Hinzufügen von Diensten zu einer App</span><span class="sxs-lookup"><span data-stu-id="a5f78-131">Add services to an app</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="a5f78-132">Konfigurieren Sie Dienste für die Dienstsammlung der App in der `Program.Main`-Methode von `Program.cs`.</span><span class="sxs-lookup"><span data-stu-id="a5f78-132">Configure services for the app's service collection in the `Program.Main` method of `Program.cs`.</span></span> <span data-ttu-id="a5f78-133">Im folgenden Beispiel ist die `MyDependency`-Implementierung für `IMyDependency` registriert:</span><span class="sxs-lookup"><span data-stu-id="a5f78-133">In the following example, the `MyDependency` implementation is registered for `IMyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program1.cs?highlight=7)]

<span data-ttu-id="a5f78-134">Nachdem der Host erstellt wurde, kann auf die Dienste vom Stammbereich der Abhängigkeitsinjektion aus zugegriffen werden, bevor Komponenten bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="a5f78-134">After the host is built, services are available from the root DI scope before any components are rendered.</span></span> <span data-ttu-id="a5f78-135">Dies kann für die Ausführung der Initialisierungslogik vor dem Rendern von Inhalten nützlich sein:</span><span class="sxs-lookup"><span data-stu-id="a5f78-135">This can be useful for running initialization logic before rendering content:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program2.cs?highlight=7,12-13)]

<span data-ttu-id="a5f78-136">Der Host stellt eine zentrale Konfigurationsinstanz für die App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="a5f78-136">The host provides a central configuration instance for the app.</span></span> <span data-ttu-id="a5f78-137">Ausgehend vom vorhergehenden Beispiel wird die URL des Wetterdiensts von einer Standardkonfigurationsquelle (z. B. `appsettings.json`) an `InitializeWeatherAsync` übergeben:</span><span class="sxs-lookup"><span data-stu-id="a5f78-137">Building on the preceding example, the weather service's URL is passed from a default configuration source (for example, `appsettings.json`) to `InitializeWeatherAsync`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/Program3.cs?highlight=13-14)]

::: zone-end

::: zone pivot="server"

<span data-ttu-id="a5f78-138">Nachdem Sie eine neue App erstellt haben, sehen Sie sich die `Startup.ConfigureServices`-Methode in `Startup.cs` an:</span><span class="sxs-lookup"><span data-stu-id="a5f78-138">After creating a new app, examine the `Startup.ConfigureServices` method in `Startup.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

...

public void ConfigureServices(IServiceCollection services)
{
    ...
}
```

<span data-ttu-id="a5f78-139">Der Methode <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> wird ein <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>-Objekt übergeben, das eine Liste von [Dienstdeskriptorobjekten](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) ist.</span><span class="sxs-lookup"><span data-stu-id="a5f78-139">The <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of [service descriptor](xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor) objects.</span></span> <span data-ttu-id="a5f78-140">Dienste werden in der `ConfigureServices`-Methode durch Bereitstellung von Dienstdeskriptoren in der Dienstsammlung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a5f78-140">Services are added in the `ConfigureServices` method by providing service descriptors to the service collection.</span></span> <span data-ttu-id="a5f78-141">Das folgende Beispiel veranschaulicht das Konzept mit der `IDataAccess`-Schnittstelle und seiner konkreten Implementierung `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="a5f78-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

::: zone-end

### <a name="service-lifetime"></a><span data-ttu-id="a5f78-142">Lebensdauer von Diensten</span><span class="sxs-lookup"><span data-stu-id="a5f78-142">Service lifetime</span></span>

<span data-ttu-id="a5f78-143">Die Dienste können mit den in der folgenden Tabelle angegebenen Lebensdauern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a5f78-143">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="a5f78-144">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="a5f78-144">Lifetime</span></span> | <span data-ttu-id="a5f78-145">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a5f78-145">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | <p><span data-ttu-id="a5f78-146">Blazor WebAssembly-Apps verfügen derzeit nicht über ein Konzept für Bereiche von Abhängigkeitsinjektionen.</span><span class="sxs-lookup"><span data-stu-id="a5f78-146">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="a5f78-147">`Scoped`-registrierte Dienste verhalten sich wie `Singleton`-Dienste.</span><span class="sxs-lookup"><span data-stu-id="a5f78-147">`Scoped`-registered services behave like `Singleton` services.</span></span></p><p><span data-ttu-id="a5f78-148">Das Blazor Server-Hostingmodell unterstützt die `Scoped`-Lebensdauer über HTTP-Anforderungen hinweg, nicht jedoch über SignalR-Verbindungs- bzw. Leitungsmeldungen zwischen Komponenten hinweg, die auf dem Client geladen sind.</span><span class="sxs-lookup"><span data-stu-id="a5f78-148">The Blazor Server hosting model supports the `Scoped` lifetime across HTTP requests but not across SingalR connection/circuit messages among components that are loaded on the client.</span></span> <span data-ttu-id="a5f78-149">Beim Navigieren zwischen Seiten oder Ansichten oder von einer Seite oder Ansicht zu einer Komponente verarbeitet der Razor Pages- bzw. MVC-Teil der App bereichsbezogene Dienste normal und erstellt die Dienste für *jede HTTP-Anforderung* neu.</span><span class="sxs-lookup"><span data-stu-id="a5f78-149">The Razor Pages or MVC portion of the app treats scoped services normally and recreates the services on *each HTTP request* when navigating among pages or views or from a page or view to a component.</span></span> <span data-ttu-id="a5f78-150">Beim Navigieren zwischen Komponenten auf dem Client werden bereichsbezogene Dienste nicht neu erstellt. In diesem Fall erfolgt die Kommunikation mit dem Server über die SignalR-Verbindung der Benutzerleitung, nicht über HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="a5f78-150">Scoped services aren't reconstructed when navigating among components on the client, where the communication to the server takes place over the SignalR connection of the user's circuit, not via HTTP requests.</span></span> <span data-ttu-id="a5f78-151">In den folgenden Komponentenszenarien auf dem Client werden bereichsbezogene Dienste neu erstellt, weil für den Benutzer eine neue Leitung erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="a5f78-151">In the following component scenarios on the client, scoped services are reconstructed because a new circuit is created for the user:</span></span></p><ul><li><span data-ttu-id="a5f78-152">Der Benutzer schließt das Browserfenster.</span><span class="sxs-lookup"><span data-stu-id="a5f78-152">The user closes the browser's window.</span></span> <span data-ttu-id="a5f78-153">Der Benutzer öffnet ein neues Fenster und navigiert zur App zurück.</span><span class="sxs-lookup"><span data-stu-id="a5f78-153">The user opens a new window and navigates back to the app.</span></span></li><li><span data-ttu-id="a5f78-154">Der Benutzer schließt die letzte Registerkarte der App in einem Browserfenster.</span><span class="sxs-lookup"><span data-stu-id="a5f78-154">The user closes the last tab of the app in a browser window.</span></span> <span data-ttu-id="a5f78-155">Der Benutzer öffnet eine neue Registerkarte und navigiert zur App zurück.</span><span class="sxs-lookup"><span data-stu-id="a5f78-155">The user opens a new tab and navigates back to the app.</span></span></li><li><span data-ttu-id="a5f78-156">Der Benutzer wählt die Schaltfläche des Browsers zum erneuten Laden oder Aktualisieren aus.</span><span class="sxs-lookup"><span data-stu-id="a5f78-156">The user selects the browser's reload/refresh button.</span></span></li></ul><p><span data-ttu-id="a5f78-157">Weitere Informationen zum Beibehalten des Benutzerstatus in Blazor Server-Apps über bereichsbezogene Dienste hinweg finden Sie unter <xref:blazor/hosting-models?pivots=server>.</span><span class="sxs-lookup"><span data-stu-id="a5f78-157">For more information on preserving user state across scoped services in Blazor Server apps, see <xref:blazor/hosting-models?pivots=server>.</span></span></p> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | <span data-ttu-id="a5f78-158">Die Abhängigkeitsinjektion erstellt eine *Einzelinstanz* des Diensts.</span><span class="sxs-lookup"><span data-stu-id="a5f78-158">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="a5f78-159">Alle Komponenten, die einen `Singleton`-Dienst erfordern, erhalten eine Instanz desselben Diensts.</span><span class="sxs-lookup"><span data-stu-id="a5f78-159">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | <span data-ttu-id="a5f78-160">Immer wenn eine Komponente eine Instanz eines `Transient`-Diensts aus dem Dienstcontainer erhält, erhält sie eine *neue Instanz* des Diensts.</span><span class="sxs-lookup"><span data-stu-id="a5f78-160">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="a5f78-161">Das Abhängigkeitsinjektionssystem basiert auf dem Abhängigkeitsinjektionssystem in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="a5f78-161">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="a5f78-162">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a5f78-162">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="a5f78-163">Anfordern eines Diensts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="a5f78-163">Request a service in a component</span></span>

<span data-ttu-id="a5f78-164">Nachdem die Dienste der Dienstsammlung hinzugefügt wurden, fügen Sie die Dienste mit der Razor-Anweisung [\@inject](xref:mvc/views/razor#inject) in die Komponenten ein.</span><span class="sxs-lookup"><span data-stu-id="a5f78-164">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="a5f78-165">[`@inject`](xref:mvc/views/razor#inject) besitzt zwei Parameter:</span><span class="sxs-lookup"><span data-stu-id="a5f78-165">[`@inject`](xref:mvc/views/razor#inject) has two parameters:</span></span>

* <span data-ttu-id="a5f78-166">Typ: Der Typ des einzufügenden Diensts.</span><span class="sxs-lookup"><span data-stu-id="a5f78-166">Type: The type of the service to inject.</span></span>
* <span data-ttu-id="a5f78-167">Eigenschaft: Der Name der Eigenschaft, die den eingefügten App-Dienst erhält.</span><span class="sxs-lookup"><span data-stu-id="a5f78-167">Property: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="a5f78-168">Die Eigenschaft erfordert keine manuelle Erstellung.</span><span class="sxs-lookup"><span data-stu-id="a5f78-168">The property doesn't require manual creation.</span></span> <span data-ttu-id="a5f78-169">Der Compiler erstellt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="a5f78-169">The compiler creates the property.</span></span>

<span data-ttu-id="a5f78-170">Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="a5f78-170">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="a5f78-171">Verwenden Sie mehrere [`@inject`](xref:mvc/views/razor#inject)-Anweisungen, um verschiedene Dienste einzufügen.</span><span class="sxs-lookup"><span data-stu-id="a5f78-171">Use multiple [`@inject`](xref:mvc/views/razor#inject) statements to inject different services.</span></span>

<span data-ttu-id="a5f78-172">Das folgende Beispiel veranschaulicht die Verwendung von [`@inject`](xref:mvc/views/razor#inject).</span><span class="sxs-lookup"><span data-stu-id="a5f78-172">The following example shows how to use [`@inject`](xref:mvc/views/razor#inject).</span></span> <span data-ttu-id="a5f78-173">Der Dienst, der `Services.IDataAccess` implementiert, wird in die Eigenschaft `DataRepository` der Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="a5f78-173">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="a5f78-174">Beachten Sie, dass der Code nur die Abstraktion `IDataAccess` verwendet:</span><span class="sxs-lookup"><span data-stu-id="a5f78-174">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/CustomerList.razor?highlight=2-3,20)]

<span data-ttu-id="a5f78-175">Intern verwendet die generierte Eigenschaft (`DataRepository`) das Attribut [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).</span><span class="sxs-lookup"><span data-stu-id="a5f78-175">Internally, the generated property (`DataRepository`) uses the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute.</span></span> <span data-ttu-id="a5f78-176">In der Regel wird dieses Attribut nicht direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="a5f78-176">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="a5f78-177">Wenn eine Basisklasse für Komponenten erforderlich ist und die eingefügten Eigenschaften auch für die Basisklasse erforderlich sind, fügen Sie das [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut manuell hinzu:</span><span class="sxs-lookup"><span data-stu-id="a5f78-177">If a base class is required for components and injected properties are also required for the base class, manually add the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

public class ComponentBase : IComponent
{
    [Inject]
    protected IDataAccess DataRepository { get; set; }

    ...
}
```

<span data-ttu-id="a5f78-178">In Komponenten, die von der Basisklasse abgeleitet sind, ist die [`@inject`](xref:mvc/views/razor#inject)-Direktive nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a5f78-178">In components derived from the base class, the [`@inject`](xref:mvc/views/razor#inject) directive isn't required.</span></span> <span data-ttu-id="a5f78-179">Das <xref:Microsoft.AspNetCore.Components.InjectAttribute> der Basisklasse ist ausreichend:</span><span class="sxs-lookup"><span data-stu-id="a5f78-179">The <xref:Microsoft.AspNetCore.Components.InjectAttribute> of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="a5f78-180">Verwenden der Abhängigkeitsinjektion in Diensten</span><span class="sxs-lookup"><span data-stu-id="a5f78-180">Use DI in services</span></span>

<span data-ttu-id="a5f78-181">Komplexe Dienste können zusätzliche Dienste erfordern.</span><span class="sxs-lookup"><span data-stu-id="a5f78-181">Complex services might require additional services.</span></span> <span data-ttu-id="a5f78-182">Im folgenden Beispiel ist für `DataAccess` der <xref:System.Net.Http.HttpClient>-Standarddienst erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a5f78-182">In the following example, `DataAccess` requires the <xref:System.Net.Http.HttpClient> default service.</span></span> <span data-ttu-id="a5f78-183">[`@inject`](xref:mvc/views/razor#inject) (oder das [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut) ist nicht für die Verwendung in Diensten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a5f78-183">[`@inject`](xref:mvc/views/razor#inject) (or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute) isn't available for use in services.</span></span> <span data-ttu-id="a5f78-184">Stattdessen muss die *Constructor Injection* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a5f78-184">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="a5f78-185">Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a5f78-185">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="a5f78-186">Wenn die Abhängigkeitsinjektion den Dienst erstellt, erkennt sie die erforderlichen Dienste im Konstruktor und stellt sie entsprechend zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="a5f78-186">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span> <span data-ttu-id="a5f78-187">Im folgenden Beispiel empfängt der Konstruktor einen <xref:System.Net.Http.HttpClient> über die Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="a5f78-187">In the following example, the constructor receives an <xref:System.Net.Http.HttpClient> via DI.</span></span> <span data-ttu-id="a5f78-188"><xref:System.Net.Http.HttpClient> ist ein Standarddienst.</span><span class="sxs-lookup"><span data-stu-id="a5f78-188"><xref:System.Net.Http.HttpClient> is a default service.</span></span>

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

<span data-ttu-id="a5f78-189">Voraussetzungen für die Constructor Injection:</span><span class="sxs-lookup"><span data-stu-id="a5f78-189">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="a5f78-190">Es muss einen Konstruktor geben, dessen Argumente alle von der Abhängigkeitsinjektion erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="a5f78-190">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="a5f78-191">Zusätzliche, nicht durch die Abhängigkeitsinjektion abgedeckte Parameter sind zulässig, wenn sie Standardwerte angeben.</span><span class="sxs-lookup"><span data-stu-id="a5f78-191">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="a5f78-192">Der anwendbare Konstruktor muss `public` sein.</span><span class="sxs-lookup"><span data-stu-id="a5f78-192">The applicable constructor must be `public`.</span></span>
* <span data-ttu-id="a5f78-193">Es muss ein anwendbarer Konstruktor vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="a5f78-193">One applicable constructor must exist.</span></span> <span data-ttu-id="a5f78-194">Im Falle einer Mehrdeutigkeit löst die Abhängigkeitsinjektion eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="a5f78-194">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="a5f78-195">Hilfsprogramm-Basiskomponentenklassen zur Verwaltung eines Bereichs für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="a5f78-195">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="a5f78-196">In ASP.NET Core-Apps werden bereichsbezogene Dienste in der Regel der aktuellen Anforderung zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a5f78-196">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="a5f78-197">Nachdem die Anforderung abgeschlossen ist, werden alle bereichsbezogenen oder vorübergehenden Dienste vom Abhängigkeitsinjektionssystem entsorgt.</span><span class="sxs-lookup"><span data-stu-id="a5f78-197">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="a5f78-198">In Blazor Server-Apps besteht der Anforderungsbereich für die Dauer der Clientverbindung, was dazu führen kann, dass vorübergehende und bereichsbezogene Dienste viel länger als erwartet leben.</span><span class="sxs-lookup"><span data-stu-id="a5f78-198">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span> <span data-ttu-id="a5f78-199">In Blazor WebAssembly-Apps werden Dienste, die mit einer bereichsbezogenen Lebensdauer registriert sind, als Singletons behandelt, sodass sie länger leben als bereichsbezogene Dienste in typischen ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="a5f78-199">In Blazor WebAssembly apps, services registered with a scoped lifetime are treated as singletons, so they live longer than scoped services in typical ASP.NET Core apps.</span></span>

> [!NOTE]
> <span data-ttu-id="a5f78-200">Informationen zum Erkennen verwerfbarer vorübergehender Dienste in einer App finden Sie im Abschnitt [Erkennen vorübergehender verwerfbarer Objekte](#detect-transient-disposables).</span><span class="sxs-lookup"><span data-stu-id="a5f78-200">To detect disposable transient services in an app, see the [Detect transient disposables](#detect-transient-disposables) section.</span></span>

<span data-ttu-id="a5f78-201">Ein Ansatz, der die Lebensdauer eines Diensts in Blazor-Apps begrenzt, ist die Verwendung des Typs <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span><span class="sxs-lookup"><span data-stu-id="a5f78-201">An approach that limits a service lifetime in Blazor apps is use of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type.</span></span> <span data-ttu-id="a5f78-202"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> ist ein abstrakter, von <xref:Microsoft.AspNetCore.Components.ComponentBase> abgeleiteter Typ, der einen der Lebensdauer der Komponente entsprechenden Bereich für die Abhängigkeitsinjektion erstellt.</span><span class="sxs-lookup"><span data-stu-id="a5f78-202"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract type derived from <xref:Microsoft.AspNetCore.Components.ComponentBase> that creates a DI scope corresponding to the lifetime of the component.</span></span> <span data-ttu-id="a5f78-203">Mit diesem Bereich ist es möglich, Abhängigkeitsinjektionsdienste mit einer bereichsbezogenen Lebensdauer zu nutzen und sie so lange wie die Komponente zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="a5f78-203">Using this scope, it's possible to use DI services with a scoped lifetime and have them live as long as the component.</span></span> <span data-ttu-id="a5f78-204">Wenn die Komponente zerstört wird, werden auch die Dienste des bereichsbezogenen Dienstanbieters der Komponente entsorgt.</span><span class="sxs-lookup"><span data-stu-id="a5f78-204">When the component is destroyed, services from the component's scoped service provider are disposed as well.</span></span> <span data-ttu-id="a5f78-205">Dies kann für Dienste nützlich sein, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="a5f78-205">This can be useful for services that:</span></span>

* <span data-ttu-id="a5f78-206">Sie sollten innerhalb einer Komponente wiederverwendet werden, da die vorübergehende Lebensdauer unangemessen ist.</span><span class="sxs-lookup"><span data-stu-id="a5f78-206">Should be reused within a component, as the transient lifetime is inappropriate.</span></span>
* <span data-ttu-id="a5f78-207">Sie sollten nicht komponentenübergreifend freigegeben werden, da die Singleton-Lebensdauer unangemessen ist.</span><span class="sxs-lookup"><span data-stu-id="a5f78-207">Shouldn't be shared across components, as the singleton lifetime is inappropriate.</span></span>

<span data-ttu-id="a5f78-208">Zwei Versionen des Typs <xref:Microsoft.AspNetCore.Components.OwningComponentBase> sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="a5f78-208">Two versions of the <xref:Microsoft.AspNetCore.Components.OwningComponentBase> type are available:</span></span>

* <span data-ttu-id="a5f78-209"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> ist ein abstraktes, verwerfbares, untergeordnetes Element vom Typ <xref:Microsoft.AspNetCore.Components.ComponentBase> mit einer geschützten <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>-Eigenschaft vom Typ <xref:System.IServiceProvider>.</span><span class="sxs-lookup"><span data-stu-id="a5f78-209"><xref:Microsoft.AspNetCore.Components.OwningComponentBase> is an abstract, disposable child of the <xref:Microsoft.AspNetCore.Components.ComponentBase> type with a protected <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property of type <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="a5f78-210">Dieser Anbieter kann zur Auflösung von Diensten verwendet werden, die der Lebensdauer der Komponente zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="a5f78-210">This provider can be used to resolve services that are scoped to the lifetime of the component.</span></span>

  <span data-ttu-id="a5f78-211">Abhängigkeitsinjektionsdienste, die mit [`@inject`](xref:mvc/views/razor#inject) oder dem [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut in die Komponente eingefügt werden, werden nicht im Gültigkeitsbereich der Komponente erstellt.</span><span class="sxs-lookup"><span data-stu-id="a5f78-211">DI services injected into the component using [`@inject`](xref:mvc/views/razor#inject) or the [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute) attribute aren't created in the component's scope.</span></span> <span data-ttu-id="a5f78-212">Um den Bereich der Komponente zu verwenden, müssen die Dienste mit <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> oder <xref:System.IServiceProvider.GetService%2A> aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="a5f78-212">To use the component's scope, services must be resolved using <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> or <xref:System.IServiceProvider.GetService%2A>.</span></span> <span data-ttu-id="a5f78-213">Allen Diensten, die unter Verwendung des <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>-Anbieters aufgelöst werden, werden ihre Abhängigkeiten aus demselben Bereich bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a5f78-213">Any services resolved using the <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> provider have their dependencies provided from that same scope.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Preferences.razor?highlight=3,20-21)]

* <span data-ttu-id="a5f78-214"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> ist abgeleitet von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> und fügt eine Eigenschaft <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> hinzu, die eine Instanz von `T` vom bereichsbezogenen Abhängigkeitsinjektionsanbieter zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="a5f78-214"><xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> derives from <xref:Microsoft.AspNetCore.Components.OwningComponentBase> and adds a <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> property that returns an instance of `T` from the scoped DI provider.</span></span> <span data-ttu-id="a5f78-215">Dieser Typ ist eine komfortable Methode für den Zugriff auf bereichsbezogene Dienste, ohne eine Instanz von <xref:System.IServiceProvider> zu verwenden, wenn es einen primären Dienst gibt, den die Anwendung aus dem Abhängigkeitsinjektionscontainer unter Verwendung des Bereichs der Komponente benötigt.</span><span class="sxs-lookup"><span data-stu-id="a5f78-215">This type is a convenient way to access scoped services without using an instance of <xref:System.IServiceProvider> when there's one primary service the app requires from the DI container using the component's scope.</span></span> <span data-ttu-id="a5f78-216">Die Eigenschaft <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> ist verfügbar, sodass die App bei Bedarf auch Dienste mit anderen Typen erhalten kann.</span><span class="sxs-lookup"><span data-stu-id="a5f78-216">The <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> property is available, so the app can get services of other types, if necessary.</span></span>

  [!code-razor[](dependency-injection/samples_snapshot/Users.razor?highlight=3,5,8)]

## <a name="use-of-an-entity-framework-core-ef-core-dbcontext-from-di"></a><span data-ttu-id="a5f78-217">Verwenden eines DbContext von Entity Framework Core (EF Core) aus DI</span><span class="sxs-lookup"><span data-stu-id="a5f78-217">Use of an Entity Framework Core (EF Core) DbContext from DI</span></span>

<span data-ttu-id="a5f78-218">Weitere Informationen finden Sie unter <xref:blazor/blazor-server-ef-core>.</span><span class="sxs-lookup"><span data-stu-id="a5f78-218">For more information, see <xref:blazor/blazor-server-ef-core>.</span></span>

::: moniker range="< aspnetcore-5.0"

## <a name="detect-transient-disposables"></a><span data-ttu-id="a5f78-219">Erkennen vorübergehender verwerfbarer Dienste</span><span class="sxs-lookup"><span data-stu-id="a5f78-219">Detect transient disposables</span></span>

<span data-ttu-id="a5f78-220">In den folgenden Beispielen wird veranschaulicht, wie verwerfbare vorübergehende Dienste in einer App erkannt werden, die <xref:Microsoft.AspNetCore.Components.OwningComponentBase> verwenden sollte.</span><span class="sxs-lookup"><span data-stu-id="a5f78-220">The following examples show how to detect disposable transient services in an app that should use <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.</span></span> <span data-ttu-id="a5f78-221">Weitere Informationen finden Sie im Abschnitt [Hilfsprogramm-Basiskomponentenklassen zur Verwaltung eines Bereichs für die Abhängigkeitsinjektion](#utility-base-component-classes-to-manage-a-di-scope).</span><span class="sxs-lookup"><span data-stu-id="a5f78-221">For more information, see the [Utility base component classes to manage a DI scope](#utility-base-component-classes-to-manage-a-di-scope) section.</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="a5f78-222">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="a5f78-222">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm.cs)]

<span data-ttu-id="a5f78-223">Im folgenden Beispiel wird `TransientDisposable` erkannt (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="a5f78-223">The `TransientDisposable` in the following example is detected (`Program.cs`):</span></span>

<!-- moniker range=">= aspnetcore-5.0"

[!code-csharp[](dependency-injection/samples_snapshot/5.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

moniker-end 

moniker range="< aspnetcore-5.0" -->

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-wasm-program.cs?highlight=6,9,17,22-25)]

<!-- moniker-end -->

::: zone-end

::: zone pivot="server"

<span data-ttu-id="a5f78-224">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span><span class="sxs-lookup"><span data-stu-id="a5f78-224">`DetectIncorrectUsagesOfTransientDisposables.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server.cs)]

<span data-ttu-id="a5f78-225">Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="a5f78-225">Add the namespace for <xref:Microsoft.Extensions.DependencyInjection?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="a5f78-226">In `Program.CreateHostBuilder` von `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a5f78-226">In `Program.CreateHostBuilder` of `Program.cs`:</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-program.cs?highlight=3)]

<span data-ttu-id="a5f78-227">Im folgenden Beispiel wird `TransientDependency` erkannt (`Startup.cs`):</span><span class="sxs-lookup"><span data-stu-id="a5f78-227">The `TransientDependency` in the following example is detected (`Startup.cs`):</span></span>

[!code-csharp[](dependency-injection/samples_snapshot/3.x/transient-disposables/DetectIncorrectUsagesOfTransientDisposables-server-startup.cs?highlight=6-8,11-32)]

::: zone-end

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a5f78-228">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a5f78-228">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* [<span data-ttu-id="a5f78-229">`IDisposable`-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="a5f78-229">`IDisposable` guidance for Transient and shared instances</span></span>](xref:fundamentals/dependency-injection#idisposable-guidance-for-transient-and-shared-instances)
* <xref:mvc/views/dependency-injection>
