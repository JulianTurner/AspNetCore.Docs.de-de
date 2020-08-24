---
title: Dependency Injection in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core Dependency Injection implementiert und wie Sie dieses Muster verwenden können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ASP.NET Core Identity
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/dependency-injection
ms.openlocfilehash: ececea3c7cc2f0cdf39bbfd29feec061f9bc6764
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628793"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="17148-103">Dependency Injection in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17148-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="17148-104">Von [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="17148-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="17148-105">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="17148-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="17148-106">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="17148-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="17148-107">Weitere Informationen zur Dependency Injection für Optionen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="17148-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="17148-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="17148-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="17148-109">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="17148-109">Overview of dependency injection</span></span>

<span data-ttu-id="17148-110">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="17148-110">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="17148-111">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="17148-111">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public void WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="17148-112">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="17148-112">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="17148-113">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="17148-113">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage(
            "IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="17148-114">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="17148-114">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="17148-115">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="17148-115">Code dependencies, such as the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="17148-116">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-116">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="17148-117">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-117">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="17148-118">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="17148-118">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="17148-119">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="17148-119">This implementation is difficult to unit test.</span></span> <span data-ttu-id="17148-120">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="17148-120">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="17148-121">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="17148-121">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="17148-122">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="17148-122">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="17148-123">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="17148-123">Registration of the dependency in a service container.</span></span> <span data-ttu-id="17148-124">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="17148-124">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="17148-125">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="17148-125">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="17148-126">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="17148-126">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="17148-127">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="17148-127">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="17148-128">In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="17148-128">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="17148-129">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="17148-129">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="17148-130">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="17148-130">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="17148-131">Die <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>-Methode registriert den Dienst mit der Lebensdauer einer einzelnen Anforderung.</span><span class="sxs-lookup"><span data-stu-id="17148-131">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="17148-132">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="17148-132">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="17148-133">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="17148-133">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="17148-134">Mit dem DI-Muster:</span><span class="sxs-lookup"><span data-stu-id="17148-134">With the DI pattern:</span></span>

* <span data-ttu-id="17148-135">Der Controller verwendet nicht den konkreten Typ `MyDependency`, sondern nur die Schnittstelle `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="17148-135">The controller doesn't use the concrete type `MyDependency`, only the interface `IMyDependency`.</span></span> <span data-ttu-id="17148-136">Dadurch kann die vom Controller verwendete Implementierung einfacher geändert werden, ohne den Controller selbst zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="17148-136">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="17148-137">Der Controller erstellt keine Instanz von `MyDependency`. Diese wird vom DI-Container erstellt.</span><span class="sxs-lookup"><span data-stu-id="17148-137">The controller doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="17148-138">Die Implementierung der `IMyDependency`-Schnittstelle kann mithilfe der integrierten Protokollierungs-API verbessert werden:</span><span class="sxs-lookup"><span data-stu-id="17148-138">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="17148-139">Die aktualisierte `ConfigureServices`-Methode registriert die neue `IMyDependency`-Implementierung:</span><span class="sxs-lookup"><span data-stu-id="17148-139">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="17148-140">`MyDependency2` erfordert, dass <xref:Microsoft.Extensions.Logging.ILogger`1> im Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="17148-140">`MyDependency2` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in the constructor.</span></span> <span data-ttu-id="17148-141">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="17148-141">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="17148-142">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="17148-142">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="17148-143">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="17148-143">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="17148-144">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="17148-144">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="17148-145">`ILogger<TCategoryName>` ist ein [vom Framework bereitgestellter Dienst](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="17148-145">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="17148-146">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch nicht mehr jeder [(generische) konstruierte Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) registriert werden muss:</span><span class="sxs-lookup"><span data-stu-id="17148-146">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="17148-147">Im Dependency-Injection-Kontext ist ein Dienst:</span><span class="sxs-lookup"><span data-stu-id="17148-147">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="17148-148">ein Objekt, das anderem App-Code einen Dienst wie `IMyDependency` bereitstellt</span><span class="sxs-lookup"><span data-stu-id="17148-148">Is typically an object that provides a service to other code in the app, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="17148-149">kein Webdienst, obwohl ein Dienst einen Webdienst verwenden kann</span><span class="sxs-lookup"><span data-stu-id="17148-149">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="17148-150">Das Framework bietet eine stabiles [Protokollierungssystem](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="17148-150">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="17148-151">Die `IMyDependency`-Implementierungen wurden geschrieben, um die DI grundlegend zu veranschaulichen, ohne die Protokollierung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="17148-151">The `IMyDependency` implementations were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="17148-152">Die meisten Apps müssen keine Protokollierungen schreiben.</span><span class="sxs-lookup"><span data-stu-id="17148-152">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="17148-153">Der folgende Code veranschaulicht die Verwendung der Standardprotokollierung, bei der keine Dienste in `ConfigureServices` registriert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="17148-153">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="17148-154">Wenn Sie den vorangehenden Code verwenden, muss `ConfigureServices` nicht aktualisiert werden, da die [Protokollierung](xref:fundamentals/logging/index) vom Framework bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="17148-154">Using the preceding code, there is no need to update `ConfigureServices` because [logging](xref:fundamentals/logging/index) is provided by the framework:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages();
}
```

## <a name="services-injected-into-startup"></a><span data-ttu-id="17148-155">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="17148-155">Services injected into Startup</span></span>

<span data-ttu-id="17148-156">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="17148-156">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="17148-157">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="17148-157">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="17148-158">Weitere Informationen finden Sie unter <xref:fundamentals/startup> und unter [Zugriffskonfiguration beim Start](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="17148-158">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="17148-159">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="17148-159">Register additional services with extension methods</span></span>

<span data-ttu-id="17148-160">Wenn die Erweiterungsmethode einer Dienstsammlung einen Dienst registrieren kann:</span><span class="sxs-lookup"><span data-stu-id="17148-160">When a service collection extension method is available to register a service:</span></span>

* <span data-ttu-id="17148-161">Üblicherweise wird eine einzelne `Add{SERVICE_NAME}`-Erweiterungsmethode verwendet, um alle für diesen Dienst erforderlichen Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="17148-161">The convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span>
* <span data-ttu-id="17148-162">Die abhängigen Dienste werden ebenfalls registriert.</span><span class="sxs-lookup"><span data-stu-id="17148-162">The dependent services are also registered.</span></span>

<span data-ttu-id="17148-163">Der folgende Code wird von der Razor Pages-Vorlage auf Grundlage einzelner Benutzerkonten generiert. Er veranschaulicht, wie mit den Erweiterungsmethoden <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> zusätzliche Dienste zum Container hinzugefügt werden können:</span><span class="sxs-lookup"><span data-stu-id="17148-163">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

<span data-ttu-id="17148-164">Weitere Informationen finden Sie unter <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> und <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="17148-164">For more information, see <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> and <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="17148-165">Im Abschnitt [Kombinieren von Dienstsammlungen](#csc) finden Sie Anweisungen zum Schreiben einer Erweiterungsmethode für die Dienstregistrierung.</span><span class="sxs-lookup"><span data-stu-id="17148-165">See the section [Combining service collection](#csc) for instructions on writing an extension method to register services.</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="17148-166">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="17148-166">Service lifetimes</span></span>

<span data-ttu-id="17148-167">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="17148-167">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="17148-168">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="17148-168">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="17148-169">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="17148-169">Transient</span></span>

<span data-ttu-id="17148-170">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="17148-170">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="17148-171">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="17148-171">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="17148-172">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="17148-172">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="17148-173">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="17148-173">Scoped</span></span>

<span data-ttu-id="17148-174">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="17148-174">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="17148-175">Wenn Sie Entity Framework Core verwenden, registriert die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterungsmethode standardmäßig `DbContext`-Typen mit einer begrenzten Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="17148-175">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="17148-176">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="17148-176">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="17148-177">Verwenden Sie bereichsbezogene Dienste in Middleware mit einem der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="17148-177">Use scoped services in middleware with one of the following approaches:</span></span>

* <span data-ttu-id="17148-178">Fügen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode ein.</span><span class="sxs-lookup"><span data-stu-id="17148-178">Inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="17148-179">Wenn Sie die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) verwenden, wird zur Laufzeit eine Ausnahme ausgelöst, da der Dienst gezwungen wird, sich wie ein Singleton zu verhalten.</span><span class="sxs-lookup"><span data-stu-id="17148-179">Injecting by [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws an exception at run time because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="17148-180">Im Beispiel für die [Lebensdauer- und Registrierungsoptionen](#lifetime-and-registration-options) wird der `InvokeAsync`-Ansatz verwendet.</span><span class="sxs-lookup"><span data-stu-id="17148-180">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) uses the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="17148-181">[Factorybezogene Middleware](<xref:fundamentals/middleware/extensibility>):</span><span class="sxs-lookup"><span data-stu-id="17148-181">[Factory-based middleware](<xref:fundamentals/middleware/extensibility>).</span></span> <span data-ttu-id="17148-182">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> überprüft, ob der registrierte Typ einer Middleware <xref:Microsoft.AspNetCore.Http.IMiddleware> implementiert.</span><span class="sxs-lookup"><span data-stu-id="17148-182"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="17148-183">Falls dies der Fall ist, wird die im Container registrierte <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>-Instanz im Container verwendet, um die <xref:Microsoft.AspNetCore.Http.IMiddleware>-Implementierung aufzulösen, anstatt die konventionsbasierte Middlewareaktivierungslogik zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="17148-183">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="17148-184">Die Middleware wird als bereichsbezogener oder vorübergehender Dienst im Dienstcontainer der App registriert.</span><span class="sxs-lookup"><span data-stu-id="17148-184">The middleware is registered as a scoped or transient service in the app's service container.</span></span>

<span data-ttu-id="17148-185">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="17148-185">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

<span data-ttu-id="17148-186">Lösen Sie einen bereichsbezogenen Dienst ***nicht*** über ein Singleton auf.</span><span class="sxs-lookup"><span data-stu-id="17148-186">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="17148-187">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="17148-187">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="17148-188">Folgendes ist zulässig:</span><span class="sxs-lookup"><span data-stu-id="17148-188">It's fine to:</span></span>

* <span data-ttu-id="17148-189">das Auflösen eines Singletondiensts aus einem bereichsbezogenen oder temporären Diensts.</span><span class="sxs-lookup"><span data-stu-id="17148-189">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="17148-190">das Auflösen eines bereichsbezogenen Diensts aus einem anderen bereichsbezogenen oder temporären Dienst</span><span class="sxs-lookup"><span data-stu-id="17148-190">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="17148-191">Standardmäßig wird in der Entwicklungsumgebung eine Ausnahme ausgelöst, wenn ein Dienst von einem anderen Dienst mit längerer Lebensdauer aufgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="17148-191">By default, in the development environment, resolving a service from another service with longer lifetime throws an exception.</span></span> <span data-ttu-id="17148-192">Weitere Informationen finden Sie unter [Bereichsvalidierung](#sv).</span><span class="sxs-lookup"><span data-stu-id="17148-192">For more information, see [Scope validation](#sv).</span></span>

### <a name="singleton"></a><span data-ttu-id="17148-193">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-193">Singleton</span></span>

<span data-ttu-id="17148-194">Dienste mit Singletonlebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden folgendermaßen erstellt:</span><span class="sxs-lookup"><span data-stu-id="17148-194">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created either:</span></span>

* <span data-ttu-id="17148-195">wenn sie zum ersten Mal angefordert werden</span><span class="sxs-lookup"><span data-stu-id="17148-195">The first time they're requested.</span></span>
* <span data-ttu-id="17148-196">vom Entwickler, wenn eine Implementierungsinstanz direkt im Container bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="17148-196">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="17148-197">(selten benötigter Ansatz)</span><span class="sxs-lookup"><span data-stu-id="17148-197">This approach is rarely needed.</span></span>

<span data-ttu-id="17148-198">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="17148-198">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="17148-199">Wenn für die App Singletonverhalten erforderlich ist, sollte der Dienstcontainer die Dienstlebensdauer verwalten.</span><span class="sxs-lookup"><span data-stu-id="17148-199">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="17148-200">Wenn Sie das Singletonentwurfsmuster implementieren, sollten Sie keinen Code zum Löschen des Singleton angeben.</span><span class="sxs-lookup"><span data-stu-id="17148-200">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="17148-201">Dienste sollten nicht durch den Code gelöscht werden können, der den Dienst aus einem Container aufgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="17148-201">Services should never be disposed by code that resolved the service from a container.</span></span> <span data-ttu-id="17148-202">Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton vom Container automatisch verworfen.</span><span class="sxs-lookup"><span data-stu-id="17148-202">If a type or factory is registered as a singleton, the container will dispose the singleton automatically.</span></span>

<span data-ttu-id="17148-203">Singletondienste müssen threadsicher sein und werden häufig in zustandslosen Diensten verwendet.</span><span class="sxs-lookup"><span data-stu-id="17148-203">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="17148-204">In Apps, die Anforderungen verarbeiten, werden Singletondienste gelöscht, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="17148-204">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="17148-205">Da der Arbeitsspeicher erst freigegeben wird, wenn die App heruntergefahren wurde, muss der Arbeitsspeicherverbrauch eines Singleton berücksichtigt werden.</span><span class="sxs-lookup"><span data-stu-id="17148-205">Because memory is not released until the app is shut down, memory use with a singleton must be considered.</span></span>

> [!WARNING]
> <span data-ttu-id="17148-206">Lösen Sie einen bereichsbezogenen Dienst ***nicht*** über ein Singleton auf.</span><span class="sxs-lookup"><span data-stu-id="17148-206">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="17148-207">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="17148-207">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="17148-208">Sie können einen Singletondienst aus einem bereichsbezogenen oder temporären Dienst auflösen.</span><span class="sxs-lookup"><span data-stu-id="17148-208">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="17148-209">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="17148-209">Service registration methods</span></span>

<span data-ttu-id="17148-210">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="17148-210">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>
<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="17148-211">Methode</span><span class="sxs-lookup"><span data-stu-id="17148-211">Method</span></span> | <span data-ttu-id="17148-212">Automatische</span><span class="sxs-lookup"><span data-stu-id="17148-212">Automatic</span></span><br><span data-ttu-id="17148-213">Objekt</span><span class="sxs-lookup"><span data-stu-id="17148-213">object</span></span><br><span data-ttu-id="17148-214">bereinigung</span><span class="sxs-lookup"><span data-stu-id="17148-214">disposal</span></span> | <span data-ttu-id="17148-215">Mehrere</span><span class="sxs-lookup"><span data-stu-id="17148-215">Multiple</span></span><br><span data-ttu-id="17148-216">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="17148-216">implementations</span></span> | <span data-ttu-id="17148-217">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="17148-217">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="17148-218">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="17148-218">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="17148-219">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-219">Yes</span></span> | <span data-ttu-id="17148-220">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-220">Yes</span></span> | <span data-ttu-id="17148-221">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-221">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="17148-222">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="17148-222">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="17148-223">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-223">Yes</span></span> | <span data-ttu-id="17148-224">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-224">Yes</span></span> | <span data-ttu-id="17148-225">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-225">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="17148-226">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="17148-226">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="17148-227">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-227">Yes</span></span> | <span data-ttu-id="17148-228">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-228">No</span></span> | <span data-ttu-id="17148-229">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-229">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="17148-230">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="17148-230">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));` | <span data-ttu-id="17148-231">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-231">No</span></span> | <span data-ttu-id="17148-232">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-232">Yes</span></span> | <span data-ttu-id="17148-233">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-233">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="17148-234">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="17148-234">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));` | <span data-ttu-id="17148-235">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-235">No</span></span> | <span data-ttu-id="17148-236">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-236">No</span></span> | <span data-ttu-id="17148-237">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-237">Yes</span></span> |

<span data-ttu-id="17148-238">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="17148-238">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="17148-239">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="17148-239">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="17148-240">`TryAdd{LIFETIME}`-Methoden registrieren den Dienst, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="17148-240">`TryAdd{LIFETIME}` methods register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="17148-241">Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="17148-241">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="17148-242">Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:</span><span class="sxs-lookup"><span data-stu-id="17148-242">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="17148-243">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="17148-243">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="17148-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst, wenn nicht bereits eine Implementierung *desselben Typs* vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="17148-244">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="17148-245">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="17148-245">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="17148-246">Beim Registrieren von Diensten sollte der Entwickler nur dann eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="17148-246">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="17148-247">Im Allgemeinen verwenden Bibliotheksautoren `TryAddEnumerable`, um zu vermeiden, dass mehrere Kopien einer Implementierung im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-247">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="17148-248">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="17148-248">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="17148-249">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="17148-249">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="17148-250">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="17148-250">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

<span data-ttu-id="17148-251">Bei der Dienstregistrierung ist die Reihenfolge unerheblich, wenn mehrere Implementierungen desselben Typs registriert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-251">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="17148-252">`IServiceCollection` ist eine Sammlung von <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span><span class="sxs-lookup"><span data-stu-id="17148-252">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>.</span></span> <span data-ttu-id="17148-253">Im folgenden Codebeispiel wird veranschaulicht, wie einem Dienst ein Konstruktor hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="17148-253">The following code shows how to add a service with a constructor:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="17148-254">Die `Add{LIFETIME}`-Methoden verwenden denselben Ansatz.</span><span class="sxs-lookup"><span data-stu-id="17148-254">The `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="17148-255">Ein Beispiel finden Sie im [Quellcode für AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="17148-255">For example, see the [source code to AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="17148-256">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="17148-256">Constructor injection behavior</span></span>

<span data-ttu-id="17148-257">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="17148-257">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="17148-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="17148-258"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="17148-259">erstellt Objekte ohne Dienstregistrierung im Dependency-Injection-Container</span><span class="sxs-lookup"><span data-stu-id="17148-259">Creates objects without service registration in the dependency injection container.</span></span>
  * <span data-ttu-id="17148-260">wird mit Frameworkfeatures wie [Taghilfsprogrammen](xref:mvc/views/tag-helpers/intro), MVC-Controllern und [Modellbindungen](xref:mvc/models/model-binding) verwendet</span><span class="sxs-lookup"><span data-stu-id="17148-260">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="17148-261">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="17148-261">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="17148-262">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="17148-262">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="17148-263">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="17148-263">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="17148-264">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="17148-264">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="17148-265">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="17148-265">Entity Framework contexts</span></span>

<span data-ttu-id="17148-266">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="17148-266">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="17148-267">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="17148-267">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="17148-268">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="17148-268">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="17148-269">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="17148-269">Lifetime and registration options</span></span>

<span data-ttu-id="17148-270">Ziehen Sie die folgenden Schnittstellen in Erwägung, die Aufgaben als Vorgänge mit einem Bezeichner (`OperationId`) darstellen, um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren.</span><span class="sxs-lookup"><span data-stu-id="17148-270">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="17148-271">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="17148-271">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="17148-272">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="17148-272">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="17148-273">Der `Operation`-Konstruktor generiert die letzten vier Zeichen einer GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="17148-273">The `Operation` constructor generates the last 4 characters of a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

-->

<span data-ttu-id="17148-274">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="17148-274">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="17148-275">Die Beispiel-App zeigt die Objektlebensdauer innerhalb von und zwischen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="17148-275">The sample app demonstrates object lifetimes within and between requests.</span></span> <span data-ttu-id="17148-276">Die `IndexModel`-Klasse der Beispiel-App und die Middleware fordern alle verfügbaren `IOperation`-Typen an und protokollieren `OperationId`:</span><span class="sxs-lookup"><span data-stu-id="17148-276">The sample app's `IndexModel` and middleware requests each kind of `IOperation` type and logs the `OperationId`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="17148-277">Die Middleware ähnelt `IndexModel` und löst dieselben Dienste auf:</span><span class="sxs-lookup"><span data-stu-id="17148-277">The middleware is similar to the `IndexModel` and resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="17148-278">Bereichsbezogene Dienste müssen in der `InvokeAsync`-Methode aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="17148-278">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="17148-279">Die Protokollierungsausgabe zeigt Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="17148-279">The logger output shows:</span></span>

* <span data-ttu-id="17148-280">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="17148-280">*Transient* objects are always different.</span></span> <span data-ttu-id="17148-281">Der temporäre Wert `OperationId` unterscheidet sich in `IndexModel` und der Middleware.</span><span class="sxs-lookup"><span data-stu-id="17148-281">The transient `OperationId` value is different in the `IndexModel` and the middleware.</span></span>
* <span data-ttu-id="17148-282">*Bereichsbezogene* Objekte sind innerhalb einer Anforderung identisch, aber unterscheiden sich für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="17148-282">*Scoped* objects are the same in each request but different across each request.</span></span>
* <span data-ttu-id="17148-283">*Singletonobjekte* sind für jede Anforderung identisch.</span><span class="sxs-lookup"><span data-stu-id="17148-283">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="17148-284">Sie können "Logging:LogLevel:Microsoft:Error" in der Datei *appsettings.Development.json* festlegen, um die Protokollierungsausgabe zu reduzieren:</span><span class="sxs-lookup"><span data-stu-id="17148-284">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="17148-285">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="17148-285">Call services from main</span></span>

<span data-ttu-id="17148-286">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="17148-286">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="17148-287">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungstasks auszuführen:</span><span class="sxs-lookup"><span data-stu-id="17148-287">This approach is useful to access a scoped service at startup to run initialization tasks:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateHostBuilder(args).Build();

        using (var scope = host.Services.CreateScope())
        {
            var services = scope.ServiceProvider;

            try
            {
                SeedData.Initialize(services);
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred seeding the DB.");
            }
        }

        host.Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="17148-288">Der vorangehende Code stammt aus dem Abschnitt [Hinzufügen des Seedinginitialisierers](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) im Tutorial zu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="17148-288">The preceding code is from [Add the seed initializer](xref:tutorials/razor-pages/sql?#add-the-seed-initializer) in the Razor Pages tutorial.</span></span>

<span data-ttu-id="17148-289">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `IMyDependency` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="17148-289">The following example shows how to obtain a context for the `IMyDependency` in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="17148-290">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="17148-290">Scope validation</span></span>

<span data-ttu-id="17148-291">Wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird und [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) aufruft, um den Host zu erstellen, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="17148-291">When the app is running in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="17148-292">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="17148-292">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="17148-293">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="17148-293">Scoped services aren't directly or indirectly injected into singletons.</span></span>
* <span data-ttu-id="17148-294">Temporäre Dienste nicht direkt oder indirekt in Singletons oder bereichsbezogene Dienste eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="17148-294">Transient services aren't directly or indirectly injected into singletons or scoped services.</span></span>

<span data-ttu-id="17148-295">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="17148-295">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="17148-296">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App, wenn der Anbieter beim Start der App erstellt und beim Herunterfahren gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="17148-296">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="17148-297">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="17148-297">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="17148-298">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer quasi auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="17148-298">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app is shut down.</span></span> <span data-ttu-id="17148-299">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="17148-299">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="17148-300">Weitere Informationen finden Sie unter [Bereichsvalidierung](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="17148-300">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="17148-301">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="17148-301">Request Services</span></span>

<span data-ttu-id="17148-302">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="17148-302">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="17148-303">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-303">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="17148-304">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="17148-304">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="17148-305">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="17148-305">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="17148-306">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="17148-306">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="17148-307">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="17148-307">This yields classes that are easier to test.</span></span>

<span data-ttu-id="17148-308">ASP.net Core erstellt einen Bereich pro Anforderung und `RequestServices` macht den bereichsbezogenen Dienstanbieter verfügbar.</span><span class="sxs-lookup"><span data-stu-id="17148-308">ASP.NET Core creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="17148-309">Alle bereichsbezogenen Dienste sind gültig, solange die Anforderung aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="17148-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="17148-310">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="17148-310">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="17148-311">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="17148-311">Design services for dependency injection</span></span>

<span data-ttu-id="17148-312">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="17148-312">Best practices are to:</span></span>

* <span data-ttu-id="17148-313">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="17148-313">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="17148-314">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="17148-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="17148-315">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="17148-315">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="17148-316">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="17148-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="17148-317">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="17148-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="17148-318">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="17148-318">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="17148-319">Wenn in eine Klasse zu viele Abhängigkeiten eingefügt wurden, bedeutet das, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) verstößt.</span><span class="sxs-lookup"><span data-stu-id="17148-319">If a class seems to have too many injected dependencies, that's generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="17148-320">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="17148-320">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="17148-321">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="17148-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="17148-322">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="17148-322">Disposal of services</span></span>

<span data-ttu-id="17148-323">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="17148-323">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="17148-324">Dienste sollten nicht durch den Code gelöscht werden können, der den Dienst aus einem Container aufgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="17148-324">Services should never be disposed by any code that resolved the service from a container.</span></span> <span data-ttu-id="17148-325">Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton vom Container verworfen.</span><span class="sxs-lookup"><span data-stu-id="17148-325">If a type or factory is registered as a singleton, the container disposes the singleton.</span></span>

<span data-ttu-id="17148-326">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="17148-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="17148-327">In der Debuggingkonsole wird nach jeder Aktualisierung der Indexseite die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="17148-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="17148-328">Nicht vom Dienstcontainer erstellte Dienste</span><span class="sxs-lookup"><span data-stu-id="17148-328">Services not created by the service container</span></span>
<!--Review: Who cares that service instances aren't disposed, singletons aren't disposed until the app shuts down anyway.
  -->
<span data-ttu-id="17148-329">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="17148-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="17148-330">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="17148-330">In the preceding code:</span></span>

* <span data-ttu-id="17148-331">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="17148-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="17148-332">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="17148-332">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="17148-333">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="17148-333">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="17148-334">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="17148-334">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="17148-335">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="17148-335">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="17148-336">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="17148-336">Transient, limited lifetime</span></span>

<span data-ttu-id="17148-337">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="17148-337">**Scenario**</span></span>

<span data-ttu-id="17148-338">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="17148-338">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="17148-339">Die Instanz wird im Stammbereich (Stammcontainer) aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="17148-339">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="17148-340">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="17148-340">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="17148-341">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="17148-341">**Solution**</span></span>

<span data-ttu-id="17148-342">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="17148-342">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="17148-343">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="17148-343">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="17148-344">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="17148-344">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="17148-345">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="17148-345">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="17148-346">Sie kann <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> verwenden, um eine Instanz außerhalb des Containers zu instanziieren und dabei den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="17148-346">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="17148-347">Freigegebene Instanz, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="17148-347">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="17148-348">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="17148-348">**Scenario**</span></span>

<span data-ttu-id="17148-349">Für die App ist eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste erforderlich, die <xref:System.IDisposable> sollte jedoch eine begrenzte Lebensdauer haben.</span><span class="sxs-lookup"><span data-stu-id="17148-349">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="17148-350">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="17148-350">**Solution**</span></span>

<span data-ttu-id="17148-351">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="17148-351">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="17148-352">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> für den Start und zum Erstellen einer neuen <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="17148-352">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="17148-353">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="17148-353">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="17148-354">Löschen Sie den Bereich, wenn die Lebensdauer beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="17148-354">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="17148-355">Allgemeine Richtlinien für IDisposable</span><span class="sxs-lookup"><span data-stu-id="17148-355">General IDisposable guidelines</span></span>

* <span data-ttu-id="17148-356">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einem vorübergehenden Gültigkeitsbereich.</span><span class="sxs-lookup"><span data-stu-id="17148-356">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="17148-357">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="17148-357">Use the factory pattern instead.</span></span>
* <span data-ttu-id="17148-358">Lösen Sie keine vorübergehenden oder bereichsbezogenen <xref:System.IDisposable>-Instanzen im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="17148-358">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="17148-359">Die einzige allgemeine Ausnahme gilt, wenn die App die <xref:System.IServiceProvider>-Instanz erstellt bzw. erneut erstellt und freigibt, was kein ideales Muster ist.</span><span class="sxs-lookup"><span data-stu-id="17148-359">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="17148-360">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="17148-360">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="17148-361">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="17148-361">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="17148-362">Bereiche sollten verwendet werden, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="17148-362">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="17148-363">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="17148-363">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="17148-364">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="17148-364">Default service container replacement</span></span>

<span data-ttu-id="17148-365">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="17148-365">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="17148-366">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="17148-366">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="17148-367">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="17148-367">Property injection</span></span>
* <span data-ttu-id="17148-368">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="17148-368">Injection based on name</span></span>
* <span data-ttu-id="17148-369">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="17148-369">Child containers</span></span>
* <span data-ttu-id="17148-370">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="17148-370">Custom lifetime management</span></span>
* <span data-ttu-id="17148-371">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="17148-371">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="17148-372">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="17148-372">Convention-based registration</span></span>

<span data-ttu-id="17148-373">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="17148-373">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="17148-374">Autofac</span><span class="sxs-lookup"><span data-stu-id="17148-374">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="17148-375">DryIoc</span><span class="sxs-lookup"><span data-stu-id="17148-375">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="17148-376">Grace</span><span class="sxs-lookup"><span data-stu-id="17148-376">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="17148-377">LightInject</span><span class="sxs-lookup"><span data-stu-id="17148-377">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="17148-378">Lamar</span><span class="sxs-lookup"><span data-stu-id="17148-378">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="17148-379">Stashbox</span><span class="sxs-lookup"><span data-stu-id="17148-379">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="17148-380">Unity</span><span class="sxs-lookup"><span data-stu-id="17148-380">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="17148-381">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="17148-381">Thread safety</span></span>

<span data-ttu-id="17148-382">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="17148-382">Create thread-safe singleton services.</span></span> <span data-ttu-id="17148-383">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="17148-383">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="17148-384">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="17148-384">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="17148-385">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="17148-385">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="17148-386">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="17148-386">Recommendations</span></span>

* <span data-ttu-id="17148-387">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="17148-387">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="17148-388">C# unterstützt keine asynchronen Konstruktoren.</span><span class="sxs-lookup"><span data-stu-id="17148-388">C# does not support asynchronous constructors.</span></span> <span data-ttu-id="17148-389">Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="17148-389">The recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="17148-390">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="17148-390">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="17148-391">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="17148-391">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="17148-392">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="17148-392">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="17148-393">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="17148-393">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="17148-394">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-394">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="17148-395">Vermeiden Sie statischen Zugriff auf Dienste.</span><span class="sxs-lookup"><span data-stu-id="17148-395">Avoid static access to services.</span></span> <span data-ttu-id="17148-396">Vermeiden Sie statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle.</span><span class="sxs-lookup"><span data-stu-id="17148-396">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>
* <span data-ttu-id="17148-397">DI-Factorys sollten schnell und synchron sein.</span><span class="sxs-lookup"><span data-stu-id="17148-397">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="17148-398">Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*.</span><span class="sxs-lookup"><span data-stu-id="17148-398">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="17148-399">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können:</span><span class="sxs-lookup"><span data-stu-id="17148-399">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="17148-400">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="17148-400">**Incorrect:**</span></span>

    ![Falscher Code](dependency-injection/_static/bad.png)

  <span data-ttu-id="17148-402">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="17148-402">**Correct**:</span></span>

  ```csharp
  public class MyClass
  {
      private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

      public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
      {
          _optionsMonitor = optionsMonitor;
      }

      public void MyMethod()
      {
          var option = _optionsMonitor.CurrentValue.Option;

          ...
      }
  }
  ```
* <span data-ttu-id="17148-403">Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst.</span><span class="sxs-lookup"><span data-stu-id="17148-403">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="17148-404">Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="17148-404">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="17148-405">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="17148-405">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="17148-406">Vermeiden Sie Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="17148-406">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="17148-407">`BuildServiceProvider` wird in der Regel aufgerufen, wenn der Entwickler einen Dienst in `ConfigureServices` auflösen möchte.</span><span class="sxs-lookup"><span data-stu-id="17148-407">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="17148-408">Nehmen Sie beispielsweise an, dass Sie `LoginPath` aus der Konfiguration abrufen müssen.</span><span class="sxs-lookup"><span data-stu-id="17148-408">For example, consider the case where you need go get the `LoginPath` from configuration.</span></span> <span data-ttu-id="17148-409">Vermeiden Sie den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="17148-409">Avoid the following code:</span></span>

  ![Ungültiger Code beim Aufruf von BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="17148-411">Wenn Sie auf die grüne Wellenlinie unter `services.BuildServiceProvider` auf der vorherigen Abbildung klicken würden, würde folgende ASP0000-Warnung angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="17148-411">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>
    * <span data-ttu-id="17148-412">ASP0000: Der Aufruf von BuildServiceProvider aus dem Anwendungscode führt dazu, dass eine zusätzliche Kopie der Singletondienste erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="17148-412">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="17148-413">Verwenden Sie Alternativen wie Dependency-Injection-Dienste als Parameter für Configure.</span><span class="sxs-lookup"><span data-stu-id="17148-413">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

   <span data-ttu-id="17148-414">Durch den Aufruf von `BuildServiceProvider` wird ein zweiter Container erstellt, der fragmentierte Singletons und Verweise auf Objektgraphen für mehrere Container verursachen kann.</span><span class="sxs-lookup"><span data-stu-id="17148-414">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span> <span data-ttu-id="17148-415">Sie sollten `LoginPath` über das DI-Optionsmuster abrufen:</span><span class="sxs-lookup"><span data-stu-id="17148-415">A correct way to get `LoginPath` is using the option pattern with DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="17148-416">Löschbare temporäre Dienste werden vom Container für die Löschung erfasst.</span><span class="sxs-lookup"><span data-stu-id="17148-416">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="17148-417">Dadurch kann es zu Arbeitsspeicherverlusten kommen, wenn diese vom obersten Container aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="17148-417">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="17148-418">Aktivieren Sie die Bereichsüberprüfung, damit die App keine bereichsbezogenen Dienste aufweist, die Singletons erfassen.</span><span class="sxs-lookup"><span data-stu-id="17148-418">Enable scope validation to make sure the app doesn't have scoped services capturing singletons.</span></span> <span data-ttu-id="17148-419">Weitere Informationen finden Sie unter [Bereichsvalidierung](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="17148-419">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="17148-420">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="17148-420">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="17148-421">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="17148-421">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="17148-422">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="17148-422">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="17148-423">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="17148-423">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="17148-424">Empfohlene Muster für Mehrinstanzenfähigkeit in der Dependency Injection (DI)</span><span class="sxs-lookup"><span data-stu-id="17148-424">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="17148-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) bietet die Mehrinstanzenfähigkeit.</span><span class="sxs-lookup"><span data-stu-id="17148-425">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="17148-426">Weitere Informationen finden Sie in der [Orchard Core-Dokumentation](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="17148-426">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="17148-427">Beispiele und Vorgehensweisen zum Erstellen modularer Apps und mehrinstanzenfähigen Apps nur mit dem Orchard Core-Framework aber ohne CMS-spezifische Features finden Sie in den Beispiel-Apps unter https://github.com/OrchardCMS/OrchardCore.Samples.</span><span class="sxs-lookup"><span data-stu-id="17148-427">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="17148-428">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="17148-428">Framework-provided services</span></span>

<span data-ttu-id="17148-429">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="17148-429">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="17148-430">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="17148-430">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="17148-431">Apps, die auf einem ASP.NET Core-Vorlagen basieren, enthalten mehr als 250 Dienste, die vom Framework registriert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-431">Apps based on an ASP.NET Core templates have more than 250 services registered by the framework.</span></span> <span data-ttu-id="17148-432">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="17148-432">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="17148-433">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="17148-433">Service Type</span></span> | <span data-ttu-id="17148-434">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="17148-434">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="17148-435">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="17148-435">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> | <span data-ttu-id="17148-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> | <span data-ttu-id="17148-437">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-437">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="17148-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="17148-439">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="17148-439">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="17148-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-440">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="17148-441">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="17148-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="17148-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="17148-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-443">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="17148-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-444">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="17148-445">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="17148-445">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="17148-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-446">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="17148-447">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-447">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="17148-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-448">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="17148-449">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="17148-449">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="17148-450">NDC-Konferenzmuster für die DI-App-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="17148-450">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="17148-451">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17148-451">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="17148-452">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="17148-452">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="17148-453">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="17148-453">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="17148-454">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="17148-454">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="17148-455">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="17148-455">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17148-456">Von [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="17148-456">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="17148-457">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="17148-457">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="17148-458">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="17148-458">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="17148-459">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="17148-459">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="17148-460">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="17148-460">Overview of dependency injection</span></span>

<span data-ttu-id="17148-461">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="17148-461">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="17148-462">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="17148-462">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

```csharp
public class MyDependency
{
    public MyDependency()
    {
    }

    public Task WriteMessage(string message)
    {
        Console.WriteLine(
            $"MyDependency.WriteMessage called. Message: {message}");

        return Task.FromResult(0);
    }
}
```

<span data-ttu-id="17148-463">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="17148-463">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="17148-464">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="17148-464">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    MyDependency _dependency = new MyDependency();

    public async Task OnGetAsync()
    {
        await _dependency.WriteMessage(
            "IndexModel.OnGetAsync created this message.");
    }
}
```

<span data-ttu-id="17148-465">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="17148-465">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="17148-466">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="17148-466">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="17148-467">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-467">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="17148-468">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-468">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="17148-469">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="17148-469">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="17148-470">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="17148-470">This implementation is difficult to unit test.</span></span> <span data-ttu-id="17148-471">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="17148-471">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="17148-472">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="17148-472">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="17148-473">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="17148-473">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="17148-474">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="17148-474">Registration of the dependency in a service container.</span></span> <span data-ttu-id="17148-475">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="17148-475">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="17148-476">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="17148-476">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="17148-477">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="17148-477">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="17148-478">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="17148-478">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="17148-479">In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="17148-479">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="17148-480">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="17148-480">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="17148-481">`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="17148-481">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="17148-482">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="17148-482">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="17148-483">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="17148-483">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="17148-484">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="17148-484">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="17148-485">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="17148-485">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="17148-486">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-486">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="17148-487">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="17148-487">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="17148-488">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="17148-488">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="17148-489">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="17148-489">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="17148-490">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="17148-490">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="17148-491">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="17148-491">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="17148-492">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="17148-492">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="17148-493">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="17148-493">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="17148-494">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="17148-494">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="17148-495">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="17148-495">We recommended that apps follow this convention.</span></span> <span data-ttu-id="17148-496">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="17148-496">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="17148-497">Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="17148-497">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

```csharp
public class MyDependency : IMyDependency
{
    public MyDependency(IConfiguration config)
    {
        var myStringValue = config["MyStringKey"];

        // Use myStringValue
    }

    ...
}
```

<span data-ttu-id="17148-498">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="17148-498">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="17148-499">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="17148-499">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="17148-500">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="17148-500">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="17148-501">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="17148-501">Services injected into Startup</span></span>

<span data-ttu-id="17148-502">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="17148-502">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="17148-503">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="17148-503">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="17148-504">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="17148-504">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="17148-505">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="17148-505">Framework-provided services</span></span>

<span data-ttu-id="17148-506">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="17148-506">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="17148-507">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="17148-507">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="17148-508">Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert.</span><span class="sxs-lookup"><span data-stu-id="17148-508">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="17148-509">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="17148-509">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="17148-510">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="17148-510">Service Type</span></span> | <span data-ttu-id="17148-511">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="17148-511">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="17148-512">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="17148-512">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="17148-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="17148-514">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-514">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="17148-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="17148-516">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="17148-516">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="17148-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-517">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="17148-518">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="17148-518">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="17148-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="17148-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-520">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="17148-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-521">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="17148-522">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="17148-522">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="17148-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-523">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="17148-524">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-524">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="17148-525">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-525">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="17148-526">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="17148-526">Register additional services with extension methods</span></span>

<span data-ttu-id="17148-527">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="17148-527">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="17148-528">Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> zusätzliche Dienste zum Container hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="17148-528">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="17148-529">Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="17148-529">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="17148-530">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="17148-530">Service lifetimes</span></span>

<span data-ttu-id="17148-531">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="17148-531">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="17148-532">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="17148-532">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="17148-533">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="17148-533">Transient</span></span>

<span data-ttu-id="17148-534">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="17148-534">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="17148-535">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="17148-535">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="17148-536">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="17148-536">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="17148-537">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="17148-537">Scoped</span></span>

<span data-ttu-id="17148-538">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="17148-538">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="17148-539">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="17148-539">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="17148-540">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="17148-540">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="17148-541">Fügen Sie ihn nicht über [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="17148-541">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="17148-542">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="17148-542">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="17148-543">Singleton</span><span class="sxs-lookup"><span data-stu-id="17148-543">Singleton</span></span>

<span data-ttu-id="17148-544">Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="17148-544">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="17148-545">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="17148-545">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="17148-546">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="17148-546">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="17148-547">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="17148-547">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="17148-548">In Apps, die Anforderungen verarbeiten, werden Singleton-Dienste verworfen, wenn der <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren der App verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="17148-548">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="17148-549">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="17148-549">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="17148-550">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="17148-550">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="17148-551">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="17148-551">Service registration methods</span></span>

<span data-ttu-id="17148-552">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="17148-552">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="17148-553">Methode</span><span class="sxs-lookup"><span data-stu-id="17148-553">Method</span></span> | <span data-ttu-id="17148-554">Automatische</span><span class="sxs-lookup"><span data-stu-id="17148-554">Automatic</span></span><br><span data-ttu-id="17148-555">Objekt</span><span class="sxs-lookup"><span data-stu-id="17148-555">object</span></span><br><span data-ttu-id="17148-556">bereinigung</span><span class="sxs-lookup"><span data-stu-id="17148-556">disposal</span></span> | <span data-ttu-id="17148-557">Mehrere</span><span class="sxs-lookup"><span data-stu-id="17148-557">Multiple</span></span><br><span data-ttu-id="17148-558">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="17148-558">implementations</span></span> | <span data-ttu-id="17148-559">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="17148-559">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="17148-560">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="17148-560">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="17148-561">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-561">Yes</span></span> | <span data-ttu-id="17148-562">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-562">Yes</span></span> | <span data-ttu-id="17148-563">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-563">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="17148-564">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="17148-564">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="17148-565">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-565">Yes</span></span> | <span data-ttu-id="17148-566">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-566">Yes</span></span> | <span data-ttu-id="17148-567">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-567">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="17148-568">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="17148-568">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="17148-569">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-569">Yes</span></span> | <span data-ttu-id="17148-570">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-570">No</span></span> | <span data-ttu-id="17148-571">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-571">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="17148-572">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="17148-572">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="17148-573">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-573">No</span></span> | <span data-ttu-id="17148-574">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-574">Yes</span></span> | <span data-ttu-id="17148-575">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-575">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="17148-576">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="17148-576">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="17148-577">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-577">No</span></span> | <span data-ttu-id="17148-578">Nein</span><span class="sxs-lookup"><span data-stu-id="17148-578">No</span></span> | <span data-ttu-id="17148-579">Ja</span><span class="sxs-lookup"><span data-stu-id="17148-579">Yes</span></span> |

<span data-ttu-id="17148-580">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="17148-580">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="17148-581">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="17148-581">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="17148-582">`TryAdd{LIFETIME}`-Methoden registrieren den Dienst nur, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="17148-582">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="17148-583">Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="17148-583">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="17148-584">Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:</span><span class="sxs-lookup"><span data-stu-id="17148-584">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="17148-585">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="17148-585">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="17148-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt.</span><span class="sxs-lookup"><span data-stu-id="17148-586">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="17148-587">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="17148-587">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="17148-588">Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="17148-588">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="17148-589">Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-589">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="17148-590">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="17148-590">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="17148-591">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="17148-591">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="17148-592">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="17148-592">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="17148-593">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="17148-593">Constructor injection behavior</span></span>

<span data-ttu-id="17148-594">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="17148-594">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="17148-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Lässt die Erstellung von Objekten ohne Dienstregistrierung im Dependency-Injection-Container zu</span><span class="sxs-lookup"><span data-stu-id="17148-595"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="17148-596">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="17148-596">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="17148-597">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="17148-597">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="17148-598">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="17148-598">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="17148-599">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="17148-599">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="17148-600">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="17148-600">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="17148-601">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="17148-601">Entity Framework contexts</span></span>

<span data-ttu-id="17148-602">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="17148-602">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="17148-603">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="17148-603">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="17148-604">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="17148-604">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="17148-605">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="17148-605">Lifetime and registration options</span></span>

<span data-ttu-id="17148-606">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="17148-606">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="17148-607">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="17148-607">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="17148-608">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="17148-608">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="17148-609">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="17148-609">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="17148-610">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="17148-610">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="17148-611">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="17148-611">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="17148-612">Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="17148-612">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="17148-613">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="17148-613">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="17148-614">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="17148-614">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="17148-615">Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung.</span><span class="sxs-lookup"><span data-stu-id="17148-615">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="17148-616">Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="17148-616">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="17148-617">Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="17148-617">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="17148-618">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="17148-618">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="17148-619">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="17148-619">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="17148-620">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="17148-620">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="17148-621">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="17148-621">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="17148-622">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="17148-622">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="17148-623">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="17148-623">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="17148-624">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="17148-624">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="17148-625">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="17148-625">**First request:**</span></span>

<span data-ttu-id="17148-626">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="17148-626">Controller operations:</span></span>

<span data-ttu-id="17148-627">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="17148-627">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="17148-628">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="17148-628">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="17148-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="17148-629">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="17148-630">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="17148-630">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="17148-631">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="17148-631">`OperationService` operations:</span></span>

<span data-ttu-id="17148-632">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="17148-632">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="17148-633">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="17148-633">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="17148-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="17148-634">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="17148-635">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="17148-635">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="17148-636">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="17148-636">**Second request:**</span></span>

<span data-ttu-id="17148-637">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="17148-637">Controller operations:</span></span>

<span data-ttu-id="17148-638">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="17148-638">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="17148-639">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="17148-639">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="17148-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="17148-640">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="17148-641">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="17148-641">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="17148-642">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="17148-642">`OperationService` operations:</span></span>

<span data-ttu-id="17148-643">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="17148-643">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="17148-644">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="17148-644">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="17148-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="17148-645">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="17148-646">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="17148-646">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="17148-647">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="17148-647">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="17148-648">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="17148-648">*Transient* objects are always different.</span></span> <span data-ttu-id="17148-649">Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="17148-649">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="17148-650">Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="17148-650">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="17148-651">*Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="17148-651">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="17148-652">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="17148-652">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="17148-653">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="17148-653">Call services from main</span></span>

<span data-ttu-id="17148-654">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="17148-654">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="17148-655">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="17148-655">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="17148-656">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="17148-656">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;

public class Program
{
    public static async Task Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();

        using (var serviceScope = host.Services.CreateScope())
        {
            var services = serviceScope.ServiceProvider;

            try
            {
                var serviceContext = services.GetRequiredService<MyScopedService>();
                // Use the context here
            }
            catch (Exception ex)
            {
                var logger = services.GetRequiredService<ILogger<Program>>();
                logger.LogError(ex, "An error occurred.");
            }
        }
    
        await host.RunAsync();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

## <a name="scope-validation"></a><span data-ttu-id="17148-657">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="17148-657">Scope validation</span></span>

<span data-ttu-id="17148-658">Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="17148-658">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="17148-659">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="17148-659">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="17148-660">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="17148-660">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="17148-661">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="17148-661">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="17148-662">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="17148-662">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="17148-663">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="17148-663">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="17148-664">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="17148-664">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="17148-665">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="17148-665">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="17148-666">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="17148-666">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="17148-667">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="17148-667">Request Services</span></span>

<span data-ttu-id="17148-668">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="17148-668">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="17148-669">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-669">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="17148-670">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="17148-670">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="17148-671">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="17148-671">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="17148-672">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="17148-672">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="17148-673">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="17148-673">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="17148-674">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="17148-674">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="17148-675">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="17148-675">Design services for dependency injection</span></span>

<span data-ttu-id="17148-676">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="17148-676">Best practices are to:</span></span>

* <span data-ttu-id="17148-677">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="17148-677">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="17148-678">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="17148-678">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="17148-679">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="17148-679">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="17148-680">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="17148-680">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="17148-681">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="17148-681">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="17148-682">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="17148-682">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="17148-683">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="17148-683">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="17148-684">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="17148-684">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="17148-685">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="17148-685">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="17148-686">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="17148-686">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="17148-687">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="17148-687">Disposal of services</span></span>

<span data-ttu-id="17148-688">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="17148-688">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="17148-689">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="17148-689">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="17148-690">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="17148-690">In the following example, the services are created by the service container and disposed automatically:</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public interface IService3 {}
public class Service3 : IService3, IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Service1>();
    services.AddSingleton<Service2>();
    services.AddSingleton<IService3>(sp => new Service3());
}
```

<span data-ttu-id="17148-691">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="17148-691">In the following example:</span></span>

* <span data-ttu-id="17148-692">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="17148-692">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="17148-693">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="17148-693">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="17148-694">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="17148-694">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="17148-695">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="17148-695">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="17148-696">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="17148-696">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="17148-697">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="17148-697">Transient, limited lifetime</span></span>

<span data-ttu-id="17148-698">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="17148-698">**Scenario**</span></span>

<span data-ttu-id="17148-699">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="17148-699">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="17148-700">Die-Instanz wird im Stammbereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="17148-700">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="17148-701">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="17148-701">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="17148-702">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="17148-702">**Solution**</span></span>

<span data-ttu-id="17148-703">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="17148-703">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="17148-704">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="17148-704">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="17148-705">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="17148-705">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="17148-706">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="17148-706">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="17148-707">Sie kann <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> verwenden, um eine Instanz außerhalb des Containers zu instanziieren und dabei den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="17148-707">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="17148-708">Freigegebene Instanz, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="17148-708">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="17148-709">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="17148-709">**Scenario**</span></span>

<span data-ttu-id="17148-710">Für die App ist eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste erforderlich, die <xref:System.IDisposable> sollte jedoch eine begrenzte Lebensdauer haben.</span><span class="sxs-lookup"><span data-stu-id="17148-710">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="17148-711">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="17148-711">**Solution**</span></span>

<span data-ttu-id="17148-712">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="17148-712">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="17148-713">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> für den Start und zum Erstellen einer neuen <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="17148-713">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="17148-714">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="17148-714">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="17148-715">Löschen Sie den Bereich, wenn die Lebensdauer beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="17148-715">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="17148-716">Allgemeine Richtlinien</span><span class="sxs-lookup"><span data-stu-id="17148-716">General Guidelines</span></span>

* <span data-ttu-id="17148-717">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einem vorübergehenden Gültigkeitsbereich.</span><span class="sxs-lookup"><span data-stu-id="17148-717">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="17148-718">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="17148-718">Use the factory pattern instead.</span></span>
* <span data-ttu-id="17148-719">Lösen Sie keine vorübergehenden oder bereichsbezogenen <xref:System.IDisposable>-Instanzen im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="17148-719">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="17148-720">Die einzige allgemeine Ausnahme gilt, wenn die App die <xref:System.IServiceProvider>-Instanz erstellt bzw. erneut erstellt und freigibt, was kein ideales Muster ist.</span><span class="sxs-lookup"><span data-stu-id="17148-720">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="17148-721">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="17148-721">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="17148-722">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="17148-722">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="17148-723">Bereiche sollten verwendet werden, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="17148-723">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="17148-724">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="17148-724">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="17148-725">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="17148-725">Default service container replacement</span></span>

<span data-ttu-id="17148-726">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="17148-726">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="17148-727">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="17148-727">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="17148-728">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="17148-728">Property injection</span></span>
* <span data-ttu-id="17148-729">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="17148-729">Injection based on name</span></span>
* <span data-ttu-id="17148-730">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="17148-730">Child containers</span></span>
* <span data-ttu-id="17148-731">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="17148-731">Custom lifetime management</span></span>
* <span data-ttu-id="17148-732">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="17148-732">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="17148-733">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="17148-733">Convention-based registration</span></span>

<span data-ttu-id="17148-734">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="17148-734">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="17148-735">Autofac</span><span class="sxs-lookup"><span data-stu-id="17148-735">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="17148-736">DryIoc</span><span class="sxs-lookup"><span data-stu-id="17148-736">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="17148-737">Grace</span><span class="sxs-lookup"><span data-stu-id="17148-737">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="17148-738">LightInject</span><span class="sxs-lookup"><span data-stu-id="17148-738">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="17148-739">Lamar</span><span class="sxs-lookup"><span data-stu-id="17148-739">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="17148-740">Stashbox</span><span class="sxs-lookup"><span data-stu-id="17148-740">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="17148-741">Unity</span><span class="sxs-lookup"><span data-stu-id="17148-741">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="17148-742">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="17148-742">Thread safety</span></span>

<span data-ttu-id="17148-743">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="17148-743">Create thread-safe singleton services.</span></span> <span data-ttu-id="17148-744">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="17148-744">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="17148-745">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="17148-745">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="17148-746">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="17148-746">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="17148-747">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="17148-747">Recommendations</span></span>

* <span data-ttu-id="17148-748">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="17148-748">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="17148-749">C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="17148-749">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="17148-750">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="17148-750">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="17148-751">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="17148-751">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="17148-752">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="17148-752">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="17148-753">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="17148-753">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="17148-754">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="17148-754">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="17148-755">Vermeiden Sie statischen Zugriff auf Dienste.</span><span class="sxs-lookup"><span data-stu-id="17148-755">Avoid static access to services.</span></span> <span data-ttu-id="17148-756">Vermeiden Sie statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle.</span><span class="sxs-lookup"><span data-stu-id="17148-756">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="17148-757">Vermeiden Sie die Verwendung des *Dienstlocatormusters*, da dieses [Steuerungsumkehrungsstrategien](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) vermischt.</span><span class="sxs-lookup"><span data-stu-id="17148-757">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="17148-758">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Abhängigkeitsinjektion verwenden können:</span><span class="sxs-lookup"><span data-stu-id="17148-758">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="17148-759">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="17148-759">**Incorrect:**</span></span>

    ```csharp
    public class MyClass()
   
      public void MyMethod()
      {
          var optionsMonitor = 
              _services.GetService<IOptionsMonitor<MyOptions>>();
          var option = optionsMonitor.CurrentValue.Option;
   
          ...
      }
      ```
   
    <span data-ttu-id="17148-760">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="17148-760">**Correct**:</span></span>

    ```csharp
    public class MyClass
    {
        private readonly IOptionsMonitor<MyOptions> _optionsMonitor;

        public MyClass(IOptionsMonitor<MyOptions> optionsMonitor)
        {
            _optionsMonitor = optionsMonitor;
        }

        public void MyMethod()
        {
            var option = _optionsMonitor.CurrentValue.Option;

            ...
        }
    }
    ```

* <span data-ttu-id="17148-761">Vermeiden Sie die Injektion von Factorys, die Abhängigkeiten zur Laufzeit mit <xref:System.IServiceProvider.GetService*> auflösen.</span><span class="sxs-lookup"><span data-stu-id="17148-761">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="17148-762">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="17148-762">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="17148-763">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="17148-763">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="17148-764">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="17148-764">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="17148-765">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="17148-765">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="17148-766">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="17148-766">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17148-767">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="17148-767">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="17148-768">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="17148-768">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="17148-769">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="17148-769">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="17148-770">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="17148-770">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="17148-771">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="17148-771">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="17148-772">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="17148-772">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
