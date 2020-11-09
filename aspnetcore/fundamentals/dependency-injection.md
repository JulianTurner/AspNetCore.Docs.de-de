---
title: Dependency Injection in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core Dependency Injection implementiert und wie Sie dieses Muster verwenden können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- 'ASP.NET Core Identity'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 6f677cc4fc26eb9d50ab6e149b7363079ae756a9
ms.sourcegitcommit: c06a5bf419541d17595af30e4cf6f2787c21855e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678566"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="0666b-103">Dependency Injection in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0666b-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0666b-104">Von [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="0666b-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="0666b-105">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0666b-106">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="0666b-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0666b-107">Informationen zum Verwenden der Dependency Injection in Konsolen-Apps finden Sie unter [Dependency Injection in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0666b-107">For information on using dependency injection in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="0666b-108">Weitere Informationen zur Dependency Injection für Optionen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="0666b-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="0666b-109">Dieses Thema enthält Informationen zur Dependency Injection in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0666b-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="0666b-110">Informationen zur Verwendung der Dependency Injection in Konsolen-Apps finden Sie unter [Dependency Injection in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="0666b-110">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="0666b-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0666b-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0666b-112">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="0666b-112">Overview of dependency injection</span></span>

<span data-ttu-id="0666b-113">Eine *Abhängigkeit* ist ein Objekt, von dem ein anderes Objekt abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="0666b-114">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="0666b-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="0666b-115">Eine Klasse kann eine Instanz der `MyDependency`-Klasse erstellen, um die `WriteMessage`-Methode zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="0666b-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="0666b-116">Im folgenden Beispiel ist die `MyDependency`-Klasse eine Abhängigkeit der `IndexModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="0666b-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly MyDependency _dependency = new MyDependency();

    public void OnGet()
    {
        _dependency.WriteMessage("IndexModel.OnGet created this message.");
    }
}
```

<span data-ttu-id="0666b-117">Die Klasse erstellt die `MyDependency`-Klasse und weist eine direkte Abhängigkeit von dieser auf.</span><span class="sxs-lookup"><span data-stu-id="0666b-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="0666b-118">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0666b-119">Die `IndexModel`-Klasse muss geändert werden, um `MyDependency` durch eine andere Implementierung zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="0666b-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="0666b-120">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese ebenfalls von der `IndexModel`-Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="0666b-121">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="0666b-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0666b-122">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="0666b-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0666b-123">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0666b-124">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="0666b-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0666b-125">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="0666b-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0666b-126">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="0666b-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0666b-127">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="0666b-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0666b-128">Dienste werden üblicherweise in der `Startup.ConfigureServices`-Methode der App registriert.</span><span class="sxs-lookup"><span data-stu-id="0666b-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0666b-129">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0666b-130">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0666b-131">In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle die `WriteMessage`-Methode:</span><span class="sxs-lookup"><span data-stu-id="0666b-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0666b-132">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="0666b-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0666b-133">Die Beispiel-App registriert den `IMyDependency`-Dienst mit dem konkreten Typ `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0666b-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0666b-134">Die <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>-Methode registriert den Dienst mit der Lebensdauer einer einzelnen Anforderung.</span><span class="sxs-lookup"><span data-stu-id="0666b-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="0666b-135">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="0666b-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="0666b-136">In der Beispiel-App wird der `IMyDependency`-Dienst angefordert und zum Aufrufen der `WriteMessage`-Methode verwendet:</span><span class="sxs-lookup"><span data-stu-id="0666b-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="0666b-137">Bei Verwendung des Dependency-Injection-Musters geht der Controller wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="0666b-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="0666b-138">Er verwendet nicht den konkreten Typ `MyDependency`, nur die von diesem implementierte `IMyDependency`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="0666b-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="0666b-139">Dadurch kann die vom Controller verwendete Implementierung einfacher geändert werden, ohne den Controller selbst zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="0666b-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="0666b-140">Er erstellt keine Instanz von `MyDependency`. Diese wird vom DI-Container erstellt.</span><span class="sxs-lookup"><span data-stu-id="0666b-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="0666b-141">Die Implementierung der `IMyDependency`-Schnittstelle kann mithilfe der integrierten Protokollierungs-API verbessert werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="0666b-142">Die aktualisierte `ConfigureServices`-Methode registriert die neue `IMyDependency`-Implementierung:</span><span class="sxs-lookup"><span data-stu-id="0666b-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="0666b-143">`MyDependency2` hängt von der <xref:Microsoft.Extensions.Logging.ILogger%601>-Schnittstelle ab, die im Konstruktor angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="0666b-144">`ILogger<TCategoryName>` ist ein [vom Framework bereitgestellter Dienst](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="0666b-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="0666b-145">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="0666b-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0666b-146">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="0666b-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0666b-147">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="0666b-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0666b-148">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur* , *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="0666b-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="0666b-149">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch nicht mehr jeder [(generische) konstruierte Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) registriert werden muss:</span><span class="sxs-lookup"><span data-stu-id="0666b-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="0666b-150">Im Dependency-Injection-Kontext ist ein Dienst:</span><span class="sxs-lookup"><span data-stu-id="0666b-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="0666b-151">in der Regel ein Objekt, das einen Dienst für andere Objekte bereitstellt, z. B. den `IMyDependency`-Dienst.</span><span class="sxs-lookup"><span data-stu-id="0666b-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="0666b-152">kein Webdienst, obwohl ein Dienst einen Webdienst verwenden kann</span><span class="sxs-lookup"><span data-stu-id="0666b-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="0666b-153">Das Framework bietet eine stabiles [Protokollierungssystem](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="0666b-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="0666b-154">Die in den vorherigen Beispielen veranschaulichten `IMyDependency`-Implementierungen wurden geschrieben, um die grundlegende Dependency Injection zu demonstrieren, nicht zum Implementieren der Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="0666b-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="0666b-155">Die meisten Apps müssen keine Protokollierungen schreiben.</span><span class="sxs-lookup"><span data-stu-id="0666b-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="0666b-156">Der folgende Code veranschaulicht die Verwendung der Standardprotokollierung, bei der keine Dienste in `ConfigureServices` registriert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="0666b-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="0666b-157">Bei Verwendung des vorangehenden Codes muss `ConfigureServices` nicht aktualisiert werden, weil die [Protokollierung](xref:fundamentals/logging/index) vom Framework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="0666b-158">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="0666b-158">Services injected into Startup</span></span>

<span data-ttu-id="0666b-159">Dienste können in den `Startup`-Konstruktor und die `Startup.Configure`-Methode eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="0666b-160">Nur die folgenden Dienste können in den `Startup`-Konstruktor eingefügt werden, wenn der generische Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="0666b-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0666b-161">Jeder Dienst, der mit dem DI-Container registriert wurde, kann in die `Startup.Configure`-Methode eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="0666b-162">Weitere Informationen finden Sie unter <xref:fundamentals/startup> und unter [Zugriffskonfiguration beim Start](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="0666b-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="0666b-163">Registrieren von Dienstgruppen mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="0666b-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="0666b-164">Das ASP.NET Core-Framework verwendet eine Konvention zum Registrieren einer Gruppe verwandter Dienste.</span><span class="sxs-lookup"><span data-stu-id="0666b-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="0666b-165">Die Konvention besteht darin, eine einzelne `Add{GROUP_NAME}`-Erweiterungsmethode zum Registrieren aller Dienste zu verwenden, die von einem Frameworkfeature benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="0666b-166">Beispielsweise registriert die Erweiterungsmethode <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> die Dienste, die für MVC-Controller erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="0666b-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="0666b-167">Der folgende Code wird von der Razor Pages-Vorlage auf Grundlage einzelner Benutzerkonten generiert. Er veranschaulicht, wie mit den Erweiterungsmethoden <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> zusätzliche Dienste zum Container hinzugefügt werden können:</span><span class="sxs-lookup"><span data-stu-id="0666b-167">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="0666b-168">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="0666b-168">Service lifetimes</span></span>

<span data-ttu-id="0666b-169">Dienste können mit einer den folgenden Lebensdaueroptionen registriert werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-169">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="0666b-170">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-170">Transient</span></span>
* <span data-ttu-id="0666b-171">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="0666b-171">Scoped</span></span>
* <span data-ttu-id="0666b-172">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-172">Singleton</span></span>

<span data-ttu-id="0666b-173">In den folgenden Abschnitten werden die einzelnen genannten Lebensdaueroptionen beschrieben.</span><span class="sxs-lookup"><span data-stu-id="0666b-173">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="0666b-174">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="0666b-174">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="0666b-175">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-175">Transient</span></span>

<span data-ttu-id="0666b-176">Kurzlebige Dienste werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="0666b-176">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="0666b-177">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="0666b-177">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="0666b-178">Vorübergehende Dienste werden mit <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> registriert.</span><span class="sxs-lookup"><span data-stu-id="0666b-178">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="0666b-179">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="0666b-179">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="0666b-180">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="0666b-180">Scoped</span></span>

<span data-ttu-id="0666b-181">Dienste mit bereichsbezogener Lebensdauer werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="0666b-181">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="0666b-182">Bereichsbezogene Dienste werden mit <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> registriert.</span><span class="sxs-lookup"><span data-stu-id="0666b-182">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="0666b-183">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="0666b-183">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="0666b-184">Wenn Sie Entity Framework Core verwenden, registriert die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterungsmethode standardmäßig `DbContext`-Typen mit einer begrenzten Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="0666b-184">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="0666b-185">Lösen Sie einen bereichsbezogenen Dienst \* **nicht** _ über einen Singleton auf, und achten Sie darauf, dies auch nicht indirekt auszuführen, z. B. über einen vorübergehenden Dienst.</span><span class="sxs-lookup"><span data-stu-id="0666b-185">Do \* **not** _ resolve a scoped service from a singleton and be careful not to do so indirectly, for example, through a transient service.</span></span> <span data-ttu-id="0666b-186">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="0666b-186">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="0666b-187">Folgendes ist zulässig:</span><span class="sxs-lookup"><span data-stu-id="0666b-187">It's fine to:</span></span>

<span data-ttu-id="0666b-188">_ Auflösen eines Singletondiensts aus einem bereichsbezogenen oder temporären Dienst</span><span class="sxs-lookup"><span data-stu-id="0666b-188">_ Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="0666b-189">das Auflösen eines bereichsbezogenen Diensts aus einem anderen bereichsbezogenen oder temporären Dienst</span><span class="sxs-lookup"><span data-stu-id="0666b-189">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="0666b-190">Standardmäßig wird in der Entwicklungsumgebung eine Ausnahme ausgelöst, wenn ein Dienst von einem anderen Dienst mit längerer Lebensdauer aufgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-190">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="0666b-191">Weitere Informationen finden Sie unter [Bereichsvalidierung](#sv).</span><span class="sxs-lookup"><span data-stu-id="0666b-191">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="0666b-192">Zum Verwenden bereichsbezogener Dienste in Middleware, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="0666b-192">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="0666b-193">Fügen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode der Middleware ein.</span><span class="sxs-lookup"><span data-stu-id="0666b-193">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0666b-194">Bei Verwendung der [Constructor Injection](xref:mvc/controllers/dependency-injection#constructor-injection) wird eine Runtimeausnahme ausgelöst, weil dem bereichsbezogenen Dienst das Verhalten eines Singletons aufgezwungen wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-194">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="0666b-195">Im Beispiel des Abschnitts [Lebensdauer und Registrierungsoptionen](#lifetime-and-registration-options) wird der `InvokeAsync`-Ansatz veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="0666b-195">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="0666b-196">Verwenden Sie [factorybezogene Middleware](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="0666b-196">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="0666b-197">Middleware, die mit diesem Ansatz registriert wurde, wird pro Clientanforderung (Verbindung) aktiviert, wodurch bereichsbezogene Dienste in die `InvokeAsync`-Methode der Middleware eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="0666b-197">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="0666b-198">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0666b-198">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0666b-199">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-199">Singleton</span></span>

<span data-ttu-id="0666b-200">Dienste mit Singletonlebensdauer werden erstellt:</span><span class="sxs-lookup"><span data-stu-id="0666b-200">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="0666b-201">wenn sie zum ersten Mal angefordert werden</span><span class="sxs-lookup"><span data-stu-id="0666b-201">The first time they're requested.</span></span>
* <span data-ttu-id="0666b-202">vom Entwickler, wenn eine Implementierungsinstanz direkt im Container bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="0666b-202">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="0666b-203">(selten benötigter Ansatz)</span><span class="sxs-lookup"><span data-stu-id="0666b-203">This approach is rarely needed.</span></span>

<span data-ttu-id="0666b-204">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="0666b-204">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0666b-205">Wenn für die App Singletonverhalten erforderlich ist, sollte der Dienstcontainer die Dienstlebensdauer verwalten.</span><span class="sxs-lookup"><span data-stu-id="0666b-205">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="0666b-206">Wenn Sie das Singletonentwurfsmuster implementieren, sollten Sie keinen Code zum Löschen des Singleton angeben.</span><span class="sxs-lookup"><span data-stu-id="0666b-206">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="0666b-207">Dienste sollten nicht durch den Code gelöscht werden können, der den Dienst aus einem Container aufgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="0666b-207">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="0666b-208">Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton automatisch vom Container verworfen.</span><span class="sxs-lookup"><span data-stu-id="0666b-208">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="0666b-209">Registrieren Sie Singletondienste mit <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span><span class="sxs-lookup"><span data-stu-id="0666b-209">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="0666b-210">Singletondienste müssen threadsicher sein und werden häufig in zustandslosen Diensten verwendet.</span><span class="sxs-lookup"><span data-stu-id="0666b-210">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="0666b-211">In Apps, die Anforderungen verarbeiten, werden Singletondienste gelöscht, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-211">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="0666b-212">Da der Arbeitsspeicher erst freigegeben wird, wenn die App heruntergefahren wird, müssen Sie den Arbeitsspeicherverbrauch eines Singletons berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="0666b-212">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="0666b-213">Lösen Sie einen bereichsbezogenen Dienst \* **nicht** _ über einen Singleton auf.</span><span class="sxs-lookup"><span data-stu-id="0666b-213">Do \* **not** _ resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0666b-214">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="0666b-214">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="0666b-215">Sie können einen Singletondienst aus einem bereichsbezogenen oder temporären Dienst auflösen.</span><span class="sxs-lookup"><span data-stu-id="0666b-215">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0666b-216">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="0666b-216">Service registration methods</span></span>

<span data-ttu-id="0666b-217">Das Framework stellt Erweiterungsmethoden für die Dienstregistrierung bereit, die in bestimmten Szenarios hilfreich sind:</span><span class="sxs-lookup"><span data-stu-id="0666b-217">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="0666b-218">Methode</span><span class="sxs-lookup"><span data-stu-id="0666b-218">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="0666b-219">Automatische</span><span class="sxs-lookup"><span data-stu-id="0666b-219">Automatic</span></span><br><span data-ttu-id="0666b-220">Objekt</span><span class="sxs-lookup"><span data-stu-id="0666b-220">object</span></span><br><span data-ttu-id="0666b-221">bereinigung</span><span class="sxs-lookup"><span data-stu-id="0666b-221">disposal</span></span> | <span data-ttu-id="0666b-222">Mehrere</span><span class="sxs-lookup"><span data-stu-id="0666b-222">Multiple</span></span><br><span data-ttu-id="0666b-223">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="0666b-223">implementations</span></span> | <span data-ttu-id="0666b-224">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="0666b-224">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0666b-225">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0666b-225">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="0666b-226">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-226">Yes</span></span>                             | <span data-ttu-id="0666b-227">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-227">Yes</span></span>                         | <span data-ttu-id="0666b-228">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-228">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0666b-229">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="0666b-229">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="0666b-230">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-230">Yes</span></span>                             | <span data-ttu-id="0666b-231">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-231">Yes</span></span>                         | <span data-ttu-id="0666b-232">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-232">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0666b-233">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0666b-233">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="0666b-234">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-234">Yes</span></span>                             | <span data-ttu-id="0666b-235">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-235">No</span></span>                          | <span data-ttu-id="0666b-236">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-236">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0666b-237">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="0666b-237">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="0666b-238">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-238">No</span></span>                              | <span data-ttu-id="0666b-239">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-239">Yes</span></span>                         | <span data-ttu-id="0666b-240">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-240">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0666b-241">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="0666b-241">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="0666b-242">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-242">No</span></span>                              | <span data-ttu-id="0666b-243">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-243">No</span></span>                          | <span data-ttu-id="0666b-244">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-244">Yes</span></span>       |

<span data-ttu-id="0666b-245">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="0666b-245">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0666b-246">Es ist üblich, mehrere Implementierungen zu verwenden, [wenn Typen zu Testzecken simuliert werden](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0666b-246">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0666b-247">Das Registrieren eines Diensts mit nur einem Implementierungstyp entspricht dem Registrieren dieses Diensts mit demselben Implementierungs- und Diensttyp.</span><span class="sxs-lookup"><span data-stu-id="0666b-247">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="0666b-248">Aus diesem Grund können nicht mehrere Implementierungen eines Diensts mithilfe von Methoden registriert werden, die keinen expliziten Diensttyp erwarten.</span><span class="sxs-lookup"><span data-stu-id="0666b-248">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="0666b-249">Solche Methoden können mehrere _Instanzen\* eines Diensts registrieren, die dann jedoch alle denselben *Implementierungstyp* aufweisen.</span><span class="sxs-lookup"><span data-stu-id="0666b-249">These methods can register multiple _instances\* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="0666b-250">Alle oben genannten Dienstregistrierungsmethoden können zum Registrieren mehrerer Dienstinstanzen desselben Diensttyps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-250">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="0666b-251">Im folgenden Beispiel wird `AddSingleton` zweimal mit `IMyDependency` als Diensttyp aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="0666b-251">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="0666b-252">Mit dem zweiten Aufruf von `AddSingleton` wird der vorhandene Singleton überschrieben, wenn er als `IMyDependency` aufgelöst wurde. Wenn mehrere Dienste über `IEnumerable<IMyDependency>` aufgelöst werden, wird der neue Singleton hinzugefügt und der alte beibehalten.</span><span class="sxs-lookup"><span data-stu-id="0666b-252">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="0666b-253">Dienste werden beim Auflösen mit `IEnumerable<{SERVICE}>` in der Reihenfolge angezeigt, in der sie registriert wurden.</span><span class="sxs-lookup"><span data-stu-id="0666b-253">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="0666b-254">Das Framework stellt außerdem `TryAdd{LIFETIME}`-Erweiterungsmethoden bereit, die den Dienst nur registrieren, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-254">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0666b-255">Im folgenden Beispiel registriert der `AddSingleton`-Aufruf `MyDependency` als Implementierung für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0666b-255">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="0666b-256">Der Aufruf von `TryAddSingleton` hat keine Auswirkung, da `IMyDependency` bereits eine registrierte Implementierung aufweist.</span><span class="sxs-lookup"><span data-stu-id="0666b-256">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="0666b-257">Weitere Informationen finden Sie unter</span><span class="sxs-lookup"><span data-stu-id="0666b-257">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="0666b-258">Die [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A)-Methoden registrieren den Dienst nur, wenn noch keine Implementierung *desselben Typs* vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-258">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0666b-259">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="0666b-259">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0666b-260">Beim Registrieren von Diensten sollte der Entwickler nur dann eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="0666b-260">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0666b-261">Im Allgemeinen verwenden Bibliotheksautoren `TryAddEnumerable`, um zu vermeiden, dass mehrere Kopien einer Implementierung im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-261">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="0666b-262">Im folgenden Beispiel registriert der erste `TryAddEnumerable`-Aufruf `MyDependency` als Implementierung für `IMyDependency1`.</span><span class="sxs-lookup"><span data-stu-id="0666b-262">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="0666b-263">Der zweite Aufruf registriert `MyDependency` für `IMyDependency2`.</span><span class="sxs-lookup"><span data-stu-id="0666b-263">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="0666b-264">Der dritte Aufruf hat keine Auswirkungen, da `IMyDependency1` bereits eine registrierte Implementierung von `MyDependency` aufweist:</span><span class="sxs-lookup"><span data-stu-id="0666b-264">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="0666b-265">Bei der Dienstregistrierung ist die Reihenfolge unerheblich, wenn mehrere Implementierungen desselben Typs registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-265">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="0666b-266">`IServiceCollection` ist eine Sammlung von <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>-Objekten.</span><span class="sxs-lookup"><span data-stu-id="0666b-266">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="0666b-267">Im folgenden Beispiel wird gezeigt, wie ein Dienst durch Erstellen und Hinzufügen von `ServiceDescriptor` registriert wird:</span><span class="sxs-lookup"><span data-stu-id="0666b-267">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="0666b-268">Die integrierten `Add{LIFETIME}`-Methoden verwenden denselben Ansatz.</span><span class="sxs-lookup"><span data-stu-id="0666b-268">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="0666b-269">Ein Beispiel finden Sie im [Quellcode für AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="0666b-269">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0666b-270">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="0666b-270">Constructor injection behavior</span></span>

<span data-ttu-id="0666b-271">Dienste können mithilfe der folgenden Ansätze aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-271">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0666b-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="0666b-272"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="0666b-273">erstellt Objekte, die nicht im Container registriert sind.</span><span class="sxs-lookup"><span data-stu-id="0666b-273">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="0666b-274">wird mit Frameworkfeatures wie [Taghilfsprogrammen](xref:mvc/views/tag-helpers/intro), MVC-Controllern und [Modellbindungen](xref:mvc/models/model-binding) verwendet</span><span class="sxs-lookup"><span data-stu-id="0666b-274">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="0666b-275">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="0666b-275">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0666b-276">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0666b-276">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0666b-277">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-277">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0666b-278">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="0666b-278">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0666b-279">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="0666b-279">Entity Framework contexts</span></span>

<span data-ttu-id="0666b-280">Entity Framework-Kontexte werden einem Dienstcontainer standardmäßig mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-280">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0666b-281">Legen Sie die Lebensdauer mithilfe einer <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Überladung fest, um eine andere Lebensdauer zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="0666b-281">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="0666b-282">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer Lebensdauer verwenden, die kürzer als die Lebensdauer des Diensts ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-282">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0666b-283">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="0666b-283">Lifetime and registration options</span></span>

<span data-ttu-id="0666b-284">In den folgenden Schnittstellen, die einen Task als Vorgang mit einem Bezeichner (`OperationId`) darstellen, wird der Unterschied zwischen Dienstlebensdauern und ihren Registrierungsoptionen veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="0666b-284">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="0666b-285">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="0666b-285">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0666b-286">Die folgende `Operation`-Klasse implementiert alle vorangehenden Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="0666b-286">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="0666b-287">Der `Operation`-Konstruktor generiert einen eindeutigen Bezeichner (GUID) und speichert die letzten 4 Zeichen in der `OperationId`-Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="0666b-287">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="0666b-288">Die `Startup.ConfigureServices`-Methode erstellt mehrere Registrierungen der `Operation`-Klasse entsprechend der benannten Lebensdauern:</span><span class="sxs-lookup"><span data-stu-id="0666b-288">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="0666b-289">Die Beispiel-App veranschaulicht die Objektlebensdauer sowohl in als auch zwischen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="0666b-289">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="0666b-290">`IndexModel` und die Middleware fordern jede Art von `IOperation`-Typ an und protokollieren `OperationId` für jeden Typ:</span><span class="sxs-lookup"><span data-stu-id="0666b-290">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="0666b-291">Ähnlich wie `IndexModel` löst die Middleware dieselben Dienste auf:</span><span class="sxs-lookup"><span data-stu-id="0666b-291">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="0666b-292">Bereichsbezogene Dienste müssen in der `InvokeAsync`-Methode aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-292">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="0666b-293">Die Protokollierungsausgabe zeigt Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="0666b-293">The logger output shows:</span></span>

* <span data-ttu-id="0666b-294">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="0666b-294">*Transient* objects are always different.</span></span> <span data-ttu-id="0666b-295">Der vorübergehende `OperationId`-Wert unterscheidet sich in `IndexModel` und der Middleware.</span><span class="sxs-lookup"><span data-stu-id="0666b-295">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="0666b-296">*Bereichsbezogene* Objekte sind für jede Anforderung identisch, aber unterscheiden sich für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="0666b-296">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="0666b-297">*Singletonobjekte* sind für jede Anforderung identisch.</span><span class="sxs-lookup"><span data-stu-id="0666b-297">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="0666b-298">Sie können "Logging:LogLevel:Microsoft:Error" in der Datei *appsettings.Development.json* festlegen, um die Protokollierungsausgabe zu reduzieren:</span><span class="sxs-lookup"><span data-stu-id="0666b-298">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="0666b-299">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="0666b-299">Call services from main</span></span>

<span data-ttu-id="0666b-300">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="0666b-300">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0666b-301">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0666b-301">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="0666b-302">Im folgenden Beispiel wird der Zugriff auf den bereichsbezogenen `IMyDependency`-Dienst und der Aufruf dessen `WriteMessage`-Methode in `Program.Main` veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="0666b-302">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="0666b-303">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="0666b-303">Scope validation</span></span>

<span data-ttu-id="0666b-304">Wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird und [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) aufruft, um den Host zu erstellen, stellt der Standarddienstanbieter sicher, dass:</span><span class="sxs-lookup"><span data-stu-id="0666b-304">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0666b-305">bereichsbezogene Dienste nicht vom Stammdienstanbieter aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-305">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="0666b-306">bereichsbezogene Dienste nicht in Singletons eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-306">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="0666b-307">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-307">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="0666b-308">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App, wenn der Anbieter beim Start der App erstellt und beim Herunterfahren gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-308">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0666b-309">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="0666b-309">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0666b-310">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer quasi auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-310">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="0666b-311">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-311">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0666b-312">Weitere Informationen finden Sie unter [Bereichsvalidierung](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="0666b-312">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="0666b-313">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="0666b-313">Request Services</span></span>

<span data-ttu-id="0666b-314">Die in einer ASP.NET Core-Anforderung verfügbaren Dienste werden über die Sammlung [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="0666b-314">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="0666b-315">Wenn Dienste innerhalb einer Anforderung angefordert werden, werden die Dienste und ihre Abhängigkeiten aus der `RequestServices`-Sammlung aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="0666b-315">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="0666b-316">Das Framework erstellt einen Bereich pro Anforderung, und `RequestServices` stellt den bereichsbezogenen Dienstanbieter zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="0666b-316">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="0666b-317">Alle bereichsbezogenen Dienste sind gültig, solange die Anforderung aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-317">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="0666b-318">Sie sollten das Anfordern von Abhängigkeiten als Konstruktorparameter zum Auflösen von Diensten aus der `RequestServices`-Sammlung bevorzugen.</span><span class="sxs-lookup"><span data-stu-id="0666b-318">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="0666b-319">Dies resultiert in Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="0666b-319">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0666b-320">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="0666b-320">Design services for dependency injection</span></span>

<span data-ttu-id="0666b-321">Beachten Sie Folgendes beim Entwerfen von Diensten für Dependency Injection:</span><span class="sxs-lookup"><span data-stu-id="0666b-321">When designing services for dependency injection:</span></span>

* <span data-ttu-id="0666b-322">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="0666b-322">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0666b-323">Vermeiden Sie das Erstellen eines globalen Zustands, indem Sie Apps stattdessen zur Verwendung von Singletondiensten entwerfen.</span><span class="sxs-lookup"><span data-stu-id="0666b-323">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="0666b-324">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="0666b-324">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0666b-325">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="0666b-325">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0666b-326">Erstellen Sie kleine, gut gestaltete und einfach zu testende Dienste.</span><span class="sxs-lookup"><span data-stu-id="0666b-326">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0666b-327">Wenn eine Klasse viele eingefügte Abhängigkeiten aufweist, ist dies möglicherweise ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Prinzip der einzelnen Verantwortung (SRP, Single Responsibility Principle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) verstößt.</span><span class="sxs-lookup"><span data-stu-id="0666b-327">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0666b-328">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Verantwortung in neue Klassen verschieben.</span><span class="sxs-lookup"><span data-stu-id="0666b-328">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="0666b-329">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="0666b-329">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0666b-330">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="0666b-330">Disposal of services</span></span>

<span data-ttu-id="0666b-331">Der Container ruft <xref:System.IDisposable.Dispose%2A> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="0666b-331">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0666b-332">Dienste, die aus dem Container aufgelöst werden, sollten nie vom Entwickler gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-332">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="0666b-333">Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton automatisch vom Container verworfen.</span><span class="sxs-lookup"><span data-stu-id="0666b-333">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="0666b-334">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="0666b-334">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="0666b-335">In der Debuggingkonsole wird nach jeder Aktualisierung der Indexseite die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0666b-335">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="0666b-336">Nicht vom Dienstcontainer erstellte Dienste</span><span class="sxs-lookup"><span data-stu-id="0666b-336">Services not created by the service container</span></span>

<span data-ttu-id="0666b-337">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="0666b-337">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="0666b-338">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="0666b-338">In the preceding code:</span></span>

* <span data-ttu-id="0666b-339">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="0666b-339">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0666b-340">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="0666b-340">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0666b-341">Der Entwickler ist für das Löschen der Dienste verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="0666b-341">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="0666b-342">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="0666b-342">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="0666b-343">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="0666b-343">Transient, limited lifetime</span></span>

<span data-ttu-id="0666b-344">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="0666b-344">**Scenario**</span></span>

<span data-ttu-id="0666b-345">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="0666b-345">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="0666b-346">Die Instanz wird im Stammbereich (Stammcontainer) aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="0666b-346">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="0666b-347">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="0666b-347">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="0666b-348">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="0666b-348">**Solution**</span></span>

<span data-ttu-id="0666b-349">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0666b-349">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="0666b-350">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="0666b-350">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="0666b-351">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="0666b-351">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="0666b-352">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="0666b-352">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="0666b-353">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, um die Instanz außerhalb des Containers zu instanziieren, während Sie den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="0666b-353">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="0666b-354">Freigegebene Instanz (eingeschränkte Lebensdauer)</span><span class="sxs-lookup"><span data-stu-id="0666b-354">Shared instance, limited lifetime</span></span>

<span data-ttu-id="0666b-355">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="0666b-355">**Scenario**</span></span>

<span data-ttu-id="0666b-356">Die App erfordert eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste, jedoch sollte die <xref:System.IDisposable>-Instanz eine begrenzte Lebensdauer aufweisen.</span><span class="sxs-lookup"><span data-stu-id="0666b-356">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="0666b-357">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="0666b-357">**Solution**</span></span>

<span data-ttu-id="0666b-358">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="0666b-358">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="0666b-359">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>, um eine neue <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>-Schnittstelle zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0666b-359">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="0666b-360">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="0666b-360">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="0666b-361">Löschen Sie den Bereich, wenn er nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-361">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="0666b-362">Allgemeine Richtlinien für IDisposable</span><span class="sxs-lookup"><span data-stu-id="0666b-362">General IDisposable guidelines</span></span>

* <span data-ttu-id="0666b-363">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einer vorübergehenden Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="0666b-363">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="0666b-364">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="0666b-364">Use the factory pattern instead.</span></span>
* <span data-ttu-id="0666b-365">Lösen Sie keine <xref:System.IDisposable>-Instanzen mit vorübergehender oder bereichsbezogener Lebensdauer im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="0666b-365">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="0666b-366">Die einzige Ausnahme hierfür besteht in dem Fall, dass die App <xref:System.IServiceProvider> erstellt oder neu erstellt und löscht. Dieses Muster ist jedoch nicht ideal.</span><span class="sxs-lookup"><span data-stu-id="0666b-366">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="0666b-367">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="0666b-367">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="0666b-368">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="0666b-368">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="0666b-369">Verwenden Sie Bereiche, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="0666b-369">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="0666b-370">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="0666b-370">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0666b-371">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="0666b-371">Default service container replacement</span></span>

<span data-ttu-id="0666b-372">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="0666b-372">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0666b-373">Die Verwendung der integrierten Container wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das nicht unterstützt wird, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0666b-373">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="0666b-374">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="0666b-374">Property injection</span></span>
* <span data-ttu-id="0666b-375">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="0666b-375">Injection based on name</span></span>
* <span data-ttu-id="0666b-376">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="0666b-376">Child containers</span></span>
* <span data-ttu-id="0666b-377">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="0666b-377">Custom lifetime management</span></span>
* <span data-ttu-id="0666b-378">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="0666b-378">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0666b-379">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="0666b-379">Convention-based registration</span></span>

<span data-ttu-id="0666b-380">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-380">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0666b-381">Autofac</span><span class="sxs-lookup"><span data-stu-id="0666b-381">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0666b-382">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0666b-382">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0666b-383">Grace</span><span class="sxs-lookup"><span data-stu-id="0666b-383">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0666b-384">LightInject</span><span class="sxs-lookup"><span data-stu-id="0666b-384">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0666b-385">Lamar</span><span class="sxs-lookup"><span data-stu-id="0666b-385">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0666b-386">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0666b-386">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0666b-387">Unity</span><span class="sxs-lookup"><span data-stu-id="0666b-387">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="0666b-388">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="0666b-388">Thread safety</span></span>

<span data-ttu-id="0666b-389">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="0666b-389">Create thread-safe singleton services.</span></span> <span data-ttu-id="0666b-390">Wenn ein Singletondienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="0666b-390">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="0666b-391">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="0666b-391">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0666b-392">Wie bei Konstruktoren vom Typ `static` erfolgt der Aufruf garantiert nur einmal über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="0666b-392">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0666b-393">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="0666b-393">Recommendations</span></span>

* <span data-ttu-id="0666b-394">Die auf `async/await` und `Task` basierende Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="0666b-394">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="0666b-395">Da C# asynchrone Konstruktoren nicht unterstützt, verwenden Sie asynchrone Methoden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="0666b-395">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="0666b-396">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="0666b-396">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0666b-397">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-397">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0666b-398">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-398">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0666b-399">Gleichermaßen sollten Sie „Daten enthaltende“ Objekte vermeiden, die nur dafür vorhanden sind, den Zugriff auf ein anderes Objekt zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="0666b-399">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="0666b-400">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-400">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="0666b-401">Vermeiden Sie statischen Zugriff auf Dienste.</span><span class="sxs-lookup"><span data-stu-id="0666b-401">Avoid static access to services.</span></span> <span data-ttu-id="0666b-402">Vermeiden Sie beispielsweise das Erfassen von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) als statisches Feld oder als Eigenschaft zur Verwendung an einer anderen Stelle.</span><span class="sxs-lookup"><span data-stu-id="0666b-402">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="0666b-403">DI-Factorys sollten schnell und synchron sein.</span><span class="sxs-lookup"><span data-stu-id="0666b-403">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="0666b-404">Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*.</span><span class="sxs-lookup"><span data-stu-id="0666b-404">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="0666b-405">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService%2A> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können:</span><span class="sxs-lookup"><span data-stu-id="0666b-405">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="0666b-406">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="0666b-406">**Incorrect:**</span></span>

    ![Falscher Code](dependency-injection/_static/bad.png)

  <span data-ttu-id="0666b-408">**Richtig** :</span><span class="sxs-lookup"><span data-stu-id="0666b-408">**Correct** :</span></span>

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
* <span data-ttu-id="0666b-409">Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst.</span><span class="sxs-lookup"><span data-stu-id="0666b-409">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="0666b-410">Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="0666b-410">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="0666b-411">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0666b-411">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="0666b-412">Vermeiden Sie Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0666b-412">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="0666b-413">`BuildServiceProvider` wird in der Regel aufgerufen, wenn der Entwickler einen Dienst in `ConfigureServices` auflösen möchte.</span><span class="sxs-lookup"><span data-stu-id="0666b-413">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="0666b-414">Berücksichtigen Sie beispielsweise den Fall, wenn `LoginPath` aus der Konfiguration geladen wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-414">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="0666b-415">Vermeiden Sie den folgenden Ansatz:</span><span class="sxs-lookup"><span data-stu-id="0666b-415">Avoid the following approach:</span></span>

  ![Ungültiger Code beim Aufruf von BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="0666b-417">Wenn Sie auf die grüne Wellenlinie unter `services.BuildServiceProvider` auf der vorherigen Abbildung klicken würden, würde folgende ASP0000-Warnung angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-417">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="0666b-418">ASP0000: Der Aufruf von BuildServiceProvider aus dem Anwendungscode führt dazu, dass eine zusätzliche Kopie der Singletondienste erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-418">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="0666b-419">Verwenden Sie Alternativen wie Dependency-Injection-Dienste als Parameter für Configure.</span><span class="sxs-lookup"><span data-stu-id="0666b-419">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="0666b-420">Durch den Aufruf von `BuildServiceProvider` wird ein zweiter Container erstellt, der fragmentierte Singletons und Verweise auf Objektgraphen für mehrere Container verursachen kann.</span><span class="sxs-lookup"><span data-stu-id="0666b-420">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="0666b-421">Eine korrekte Methode zum Abrufen von `LoginPath` besteht darin, die integrierte Unterstützung von DI des Optionsmusters zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="0666b-421">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="0666b-422">Löschbare temporäre Dienste werden vom Container für die Löschung erfasst.</span><span class="sxs-lookup"><span data-stu-id="0666b-422">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="0666b-423">Dadurch kann es zu Arbeitsspeicherverlusten kommen, wenn diese vom obersten Container aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-423">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="0666b-424">Aktivieren Sie die Bereichsüberprüfung, um sicherzustellen, dass die App keine Singletons aufweist, die bereichsbezogene Dienste erfassen.</span><span class="sxs-lookup"><span data-stu-id="0666b-424">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="0666b-425">Weitere Informationen finden Sie unter [Bereichsvalidierung](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="0666b-425">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="0666b-426">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="0666b-426">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0666b-427">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="0666b-427">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0666b-428">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="0666b-428">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0666b-429">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="0666b-429">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="0666b-430">Empfohlene Muster für Mehrinstanzenfähigkeit in der Dependency Injection (DI)</span><span class="sxs-lookup"><span data-stu-id="0666b-430">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="0666b-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) ist ein Anwendungsframework zum Erstellen modularer Anwendung mit mehreren Mandanten in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0666b-431">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="0666b-432">Weitere Informationen finden Sie in der [Orchard Core-Dokumentation](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="0666b-432">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="0666b-433">Beispiele zum Erstellen modularer Apps und Apps mit mehreren Mandanten nur mit dem Orchard Core-Framework und ohne den CMS-spezifischen Features finden Sie in den [Orchard Core-Beispielen](https://github.com/OrchardCMS/OrchardCore.Samples).</span><span class="sxs-lookup"><span data-stu-id="0666b-433">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0666b-434">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="0666b-434">Framework-provided services</span></span>

<span data-ttu-id="0666b-435">Die `Startup.ConfigureServices`-Methode registriert Dienste, die von der App verwendet werden, einschließlich Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0666b-435">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0666b-436">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="0666b-436">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0666b-437">Für Apps, die auf ASP.NET Core-Vorlagen basieren, registriert das Framework mehr als 250 Dienste.</span><span class="sxs-lookup"><span data-stu-id="0666b-437">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="0666b-438">In der folgenden Tabelle finden Sie eine kleine Stichprobe der vom Framework registrierten Dienste:</span><span class="sxs-lookup"><span data-stu-id="0666b-438">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="0666b-439">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="0666b-439">Service Type</span></span>                                                                                    | <span data-ttu-id="0666b-440">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="0666b-440">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0666b-441">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-441">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="0666b-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-442">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="0666b-443">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-443">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="0666b-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-444">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="0666b-445">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-445">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="0666b-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-446">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="0666b-447">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-447">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="0666b-448">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-448">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="0666b-449">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-449">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="0666b-450">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-450">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="0666b-451">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-451">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="0666b-452">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-452">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="0666b-453">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-453">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="0666b-454">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-454">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="0666b-455">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0666b-455">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="0666b-456">NDC-Konferenzmuster für die DI-App-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="0666b-456">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0666b-457">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0666b-457">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="0666b-458">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="0666b-458">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="0666b-459">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="0666b-459">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="0666b-460">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="0666b-460">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="0666b-461">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="0666b-461">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0666b-462">Von [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="0666b-462">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="0666b-463">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-463">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="0666b-464">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="0666b-464">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="0666b-465">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0666b-465">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="0666b-466">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="0666b-466">Overview of dependency injection</span></span>

<span data-ttu-id="0666b-467">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="0666b-467">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="0666b-468">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="0666b-468">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="0666b-469">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="0666b-469">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="0666b-470">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="0666b-470">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="0666b-471">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="0666b-471">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="0666b-472">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-472">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="0666b-473">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-473">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="0666b-474">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-474">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="0666b-475">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="0666b-475">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="0666b-476">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="0666b-476">This implementation is difficult to unit test.</span></span> <span data-ttu-id="0666b-477">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-477">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="0666b-478">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="0666b-478">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="0666b-479">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="0666b-479">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="0666b-480">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="0666b-480">Registration of the dependency in a service container.</span></span> <span data-ttu-id="0666b-481">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="0666b-481">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="0666b-482">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="0666b-482">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="0666b-483">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-483">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="0666b-484">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-484">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="0666b-485">In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="0666b-485">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="0666b-486">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="0666b-486">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="0666b-487">`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="0666b-487">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="0666b-488">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="0666b-488">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="0666b-489">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="0666b-489">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="0666b-490">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="0666b-490">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="0666b-491">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur* , *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="0666b-491">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="0666b-492">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-492">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="0666b-493">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="0666b-493">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0666b-494">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-494">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="0666b-495">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="0666b-495">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="0666b-496">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="0666b-496">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="0666b-497">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="0666b-497">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="0666b-498">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="0666b-498">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="0666b-499">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="0666b-499">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="0666b-500">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="0666b-500">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="0666b-501">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="0666b-501">We recommended that apps follow this convention.</span></span> <span data-ttu-id="0666b-502">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="0666b-502">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="0666b-503">Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-503">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="0666b-504">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="0666b-504">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="0666b-505">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="0666b-505">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="0666b-506">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="0666b-506">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="0666b-507">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="0666b-507">Services injected into Startup</span></span>

<span data-ttu-id="0666b-508">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-508">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="0666b-509">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-509">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="0666b-510">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0666b-510">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="0666b-511">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="0666b-511">Framework-provided services</span></span>

<span data-ttu-id="0666b-512">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="0666b-512">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="0666b-513">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="0666b-513">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="0666b-514">Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert.</span><span class="sxs-lookup"><span data-stu-id="0666b-514">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="0666b-515">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="0666b-515">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="0666b-516">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="0666b-516">Service Type</span></span> | <span data-ttu-id="0666b-517">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="0666b-517">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="0666b-518">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-518">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="0666b-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-519">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="0666b-520">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-520">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="0666b-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-521">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="0666b-522">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-522">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="0666b-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-523">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="0666b-524">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-524">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="0666b-525">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-525">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="0666b-526">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-526">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="0666b-527">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-527">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="0666b-528">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-528">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="0666b-529">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-529">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="0666b-530">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-530">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="0666b-531">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-531">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="0666b-532">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="0666b-532">Register additional services with extension methods</span></span>

<span data-ttu-id="0666b-533">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="0666b-533">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="0666b-534">Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> zusätzliche Dienste zum Container hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-534">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="0666b-535">Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="0666b-535">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="0666b-536">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="0666b-536">Service lifetimes</span></span>

<span data-ttu-id="0666b-537">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="0666b-537">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="0666b-538">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-538">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="0666b-539">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="0666b-539">Transient</span></span>

<span data-ttu-id="0666b-540">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="0666b-540">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="0666b-541">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="0666b-541">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="0666b-542">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="0666b-542">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="0666b-543">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="0666b-543">Scoped</span></span>

<span data-ttu-id="0666b-544">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="0666b-544">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="0666b-545">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="0666b-545">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="0666b-546">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="0666b-546">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="0666b-547">Fügen Sie ihn nicht über [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="0666b-547">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="0666b-548">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="0666b-548">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="0666b-549">Singleton</span><span class="sxs-lookup"><span data-stu-id="0666b-549">Singleton</span></span>

<span data-ttu-id="0666b-550">Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="0666b-550">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="0666b-551">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="0666b-551">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="0666b-552">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="0666b-552">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="0666b-553">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="0666b-553">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="0666b-554">In Apps, die Anforderungen verarbeiten, werden Singleton-Dienste verworfen, wenn der <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren der App verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-554">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="0666b-555">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="0666b-555">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="0666b-556">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="0666b-556">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="0666b-557">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="0666b-557">Service registration methods</span></span>

<span data-ttu-id="0666b-558">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="0666b-558">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="0666b-559">Methode</span><span class="sxs-lookup"><span data-stu-id="0666b-559">Method</span></span> | <span data-ttu-id="0666b-560">Automatische</span><span class="sxs-lookup"><span data-stu-id="0666b-560">Automatic</span></span><br><span data-ttu-id="0666b-561">Objekt</span><span class="sxs-lookup"><span data-stu-id="0666b-561">object</span></span><br><span data-ttu-id="0666b-562">bereinigung</span><span class="sxs-lookup"><span data-stu-id="0666b-562">disposal</span></span> | <span data-ttu-id="0666b-563">Mehrere</span><span class="sxs-lookup"><span data-stu-id="0666b-563">Multiple</span></span><br><span data-ttu-id="0666b-564">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="0666b-564">implementations</span></span> | <span data-ttu-id="0666b-565">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="0666b-565">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="0666b-566">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0666b-566">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="0666b-567">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-567">Yes</span></span> | <span data-ttu-id="0666b-568">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-568">Yes</span></span> | <span data-ttu-id="0666b-569">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-569">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="0666b-570">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="0666b-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="0666b-571">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-571">Yes</span></span> | <span data-ttu-id="0666b-572">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-572">Yes</span></span> | <span data-ttu-id="0666b-573">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-573">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="0666b-574">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0666b-574">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="0666b-575">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-575">Yes</span></span> | <span data-ttu-id="0666b-576">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-576">No</span></span> | <span data-ttu-id="0666b-577">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-577">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="0666b-578">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="0666b-578">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="0666b-579">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-579">No</span></span> | <span data-ttu-id="0666b-580">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-580">Yes</span></span> | <span data-ttu-id="0666b-581">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-581">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="0666b-582">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="0666b-582">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="0666b-583">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-583">No</span></span> | <span data-ttu-id="0666b-584">Nein</span><span class="sxs-lookup"><span data-stu-id="0666b-584">No</span></span> | <span data-ttu-id="0666b-585">Ja</span><span class="sxs-lookup"><span data-stu-id="0666b-585">Yes</span></span> |

<span data-ttu-id="0666b-586">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="0666b-586">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="0666b-587">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="0666b-587">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="0666b-588">Das Registrieren eines Diensts mit nur einem Implementierungstyp entspricht dem Registrieren dieses Diensts mit demselben Implementierungs- und Diensttyp.</span><span class="sxs-lookup"><span data-stu-id="0666b-588">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="0666b-589">Aus diesem Grund können nicht mehrere Implementierungen eines Diensts mithilfe von Methoden registriert werden, die keinen expliziten Diensttyp erwarten.</span><span class="sxs-lookup"><span data-stu-id="0666b-589">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="0666b-590">Solche Methoden können mehrere *Instanzen* eines Diensts registrieren, die dann jedoch alle denselben *Implementierungstyp* aufweisen.</span><span class="sxs-lookup"><span data-stu-id="0666b-590">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="0666b-591">Alle oben genannten Dienstregistrierungsmethoden können zum Registrieren mehrerer Dienstinstanzen desselben Diensttyps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-591">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="0666b-592">Im folgenden Beispiel wird `AddSingleton` zweimal mit `IMyDependency` als Diensttyp aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="0666b-592">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="0666b-593">Mit dem zweiten Aufruf von `AddSingleton` wird der vorhandene Singleton überschrieben, wenn er als `IMyDependency` aufgelöst wurde. Wenn mehrere Dienste über `IEnumerable<IMyDependency>` aufgelöst werden, wird der neue Singleton hinzugefügt und der alte beibehalten.</span><span class="sxs-lookup"><span data-stu-id="0666b-593">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="0666b-594">Dienste werden beim Auflösen mit `IEnumerable<{SERVICE}>` in der Reihenfolge angezeigt, in der sie registriert wurden.</span><span class="sxs-lookup"><span data-stu-id="0666b-594">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="0666b-595">Das Framework stellt außerdem `TryAdd{LIFETIME}`-Erweiterungsmethoden bereit, die den Dienst nur registrieren, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-595">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="0666b-596">Im folgenden Beispiel registriert der `AddSingleton`-Aufruf `MyDependency` als Implementierung für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="0666b-596">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="0666b-597">Der Aufruf von `TryAddSingleton` hat keine Auswirkung, da `IMyDependency` bereits eine registrierte Implementierung aufweist.</span><span class="sxs-lookup"><span data-stu-id="0666b-597">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumberable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="0666b-598">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="0666b-598">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="0666b-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt.</span><span class="sxs-lookup"><span data-stu-id="0666b-599">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="0666b-600">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="0666b-600">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="0666b-601">Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="0666b-601">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="0666b-602">Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-602">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="0666b-603">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="0666b-603">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="0666b-604">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="0666b-604">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="0666b-605">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="0666b-605">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="0666b-606">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="0666b-606">Constructor injection behavior</span></span>

<span data-ttu-id="0666b-607">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-607">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="0666b-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Lässt die Erstellung von Objekten ohne Dienstregistrierung im Dependency-Injection-Container zu</span><span class="sxs-lookup"><span data-stu-id="0666b-608"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="0666b-609">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="0666b-609">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="0666b-610">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="0666b-610">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="0666b-611">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="0666b-611">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="0666b-612">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-612">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="0666b-613">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="0666b-613">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="0666b-614">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="0666b-614">Entity Framework contexts</span></span>

<span data-ttu-id="0666b-615">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-615">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="0666b-616">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-616">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="0666b-617">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="0666b-617">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="0666b-618">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="0666b-618">Lifetime and registration options</span></span>

<span data-ttu-id="0666b-619">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="0666b-619">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="0666b-620">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="0666b-620">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="0666b-621">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="0666b-621">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="0666b-622">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="0666b-622">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="0666b-623">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="0666b-623">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="0666b-624">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="0666b-624">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="0666b-625">Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="0666b-625">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="0666b-626">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="0666b-626">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="0666b-627">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="0666b-627">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="0666b-628">Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung.</span><span class="sxs-lookup"><span data-stu-id="0666b-628">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="0666b-629">Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="0666b-629">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="0666b-630">Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="0666b-630">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="0666b-631">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="0666b-631">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="0666b-632">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="0666b-632">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="0666b-633">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="0666b-633">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="0666b-634">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="0666b-634">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="0666b-635">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="0666b-635">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="0666b-636">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="0666b-636">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="0666b-637">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="0666b-637">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="0666b-638">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="0666b-638">**First request:**</span></span>

<span data-ttu-id="0666b-639">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="0666b-639">Controller operations:</span></span>

<span data-ttu-id="0666b-640">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="0666b-640">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="0666b-641">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0666b-641">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0666b-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0666b-642">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0666b-643">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0666b-643">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0666b-644">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="0666b-644">`OperationService` operations:</span></span>

<span data-ttu-id="0666b-645">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="0666b-645">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="0666b-646">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="0666b-646">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="0666b-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0666b-647">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0666b-648">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0666b-648">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0666b-649">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="0666b-649">**Second request:**</span></span>

<span data-ttu-id="0666b-650">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="0666b-650">Controller operations:</span></span>

<span data-ttu-id="0666b-651">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="0666b-651">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="0666b-652">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0666b-652">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0666b-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0666b-653">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0666b-654">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0666b-654">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0666b-655">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="0666b-655">`OperationService` operations:</span></span>

<span data-ttu-id="0666b-656">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="0666b-656">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="0666b-657">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="0666b-657">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="0666b-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="0666b-658">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="0666b-659">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="0666b-659">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="0666b-660">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="0666b-660">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="0666b-661">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="0666b-661">*Transient* objects are always different.</span></span> <span data-ttu-id="0666b-662">Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="0666b-662">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="0666b-663">Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="0666b-663">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="0666b-664">*Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="0666b-664">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="0666b-665">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="0666b-665">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="0666b-666">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="0666b-666">Call services from main</span></span>

<span data-ttu-id="0666b-667">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="0666b-667">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="0666b-668">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0666b-668">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="0666b-669">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="0666b-669">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="0666b-670">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="0666b-670">Scope validation</span></span>

<span data-ttu-id="0666b-671">Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="0666b-671">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="0666b-672">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="0666b-672">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="0666b-673">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="0666b-673">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="0666b-674">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-674">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="0666b-675">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-675">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="0666b-676">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="0666b-676">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="0666b-677">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-677">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="0666b-678">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-678">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="0666b-679">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="0666b-679">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="0666b-680">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="0666b-680">Request Services</span></span>

<span data-ttu-id="0666b-681">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="0666b-681">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="0666b-682">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-682">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="0666b-683">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="0666b-683">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="0666b-684">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="0666b-684">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="0666b-685">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="0666b-685">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="0666b-686">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="0666b-686">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="0666b-687">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="0666b-687">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="0666b-688">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="0666b-688">Design services for dependency injection</span></span>

<span data-ttu-id="0666b-689">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="0666b-689">Best practices are to:</span></span>

* <span data-ttu-id="0666b-690">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="0666b-690">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="0666b-691">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="0666b-691">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="0666b-692">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="0666b-692">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="0666b-693">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="0666b-693">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="0666b-694">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="0666b-694">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="0666b-695">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="0666b-695">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="0666b-696">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="0666b-696">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="0666b-697">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="0666b-697">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="0666b-698">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="0666b-698">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="0666b-699">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="0666b-699">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="0666b-700">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="0666b-700">Disposal of services</span></span>

<span data-ttu-id="0666b-701">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="0666b-701">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="0666b-702">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="0666b-702">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="0666b-703">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="0666b-703">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="0666b-704">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0666b-704">In the following example:</span></span>

* <span data-ttu-id="0666b-705">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="0666b-705">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="0666b-706">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="0666b-706">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="0666b-707">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="0666b-707">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="0666b-708">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="0666b-708">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="0666b-709">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="0666b-709">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="0666b-710">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="0666b-710">Transient, limited lifetime</span></span>

<span data-ttu-id="0666b-711">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="0666b-711">**Scenario**</span></span>

<span data-ttu-id="0666b-712">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="0666b-712">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="0666b-713">Die-Instanz wird im Stammbereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="0666b-713">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="0666b-714">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="0666b-714">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="0666b-715">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="0666b-715">**Solution**</span></span>

<span data-ttu-id="0666b-716">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0666b-716">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="0666b-717">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="0666b-717">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="0666b-718">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="0666b-718">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="0666b-719">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="0666b-719">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="0666b-720">Sie kann <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> verwenden, um eine Instanz außerhalb des Containers zu instanziieren und dabei den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="0666b-720">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="0666b-721">Freigegebene Instanz, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="0666b-721">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="0666b-722">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="0666b-722">**Scenario**</span></span>

<span data-ttu-id="0666b-723">Für die App ist eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste erforderlich, die <xref:System.IDisposable> sollte jedoch eine begrenzte Lebensdauer haben.</span><span class="sxs-lookup"><span data-stu-id="0666b-723">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="0666b-724">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="0666b-724">**Solution**</span></span>

<span data-ttu-id="0666b-725">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="0666b-725">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="0666b-726">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> für den Start und zum Erstellen einer neuen <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="0666b-726">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="0666b-727">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="0666b-727">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="0666b-728">Löschen Sie den Bereich, wenn die Lebensdauer beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="0666b-728">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="0666b-729">Allgemeine Richtlinien</span><span class="sxs-lookup"><span data-stu-id="0666b-729">General Guidelines</span></span>

* <span data-ttu-id="0666b-730">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einem vorübergehenden Gültigkeitsbereich.</span><span class="sxs-lookup"><span data-stu-id="0666b-730">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="0666b-731">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="0666b-731">Use the factory pattern instead.</span></span>
* <span data-ttu-id="0666b-732">Lösen Sie keine vorübergehenden oder bereichsbezogenen <xref:System.IDisposable>-Instanzen im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="0666b-732">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="0666b-733">Die einzige allgemeine Ausnahme gilt, wenn die App die <xref:System.IServiceProvider>-Instanz erstellt bzw. erneut erstellt und freigibt, was kein ideales Muster ist.</span><span class="sxs-lookup"><span data-stu-id="0666b-733">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="0666b-734">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="0666b-734">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="0666b-735">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="0666b-735">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="0666b-736">Bereiche sollten verwendet werden, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="0666b-736">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="0666b-737">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="0666b-737">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="0666b-738">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="0666b-738">Default service container replacement</span></span>

<span data-ttu-id="0666b-739">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="0666b-739">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="0666b-740">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="0666b-740">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="0666b-741">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="0666b-741">Property injection</span></span>
* <span data-ttu-id="0666b-742">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="0666b-742">Injection based on name</span></span>
* <span data-ttu-id="0666b-743">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="0666b-743">Child containers</span></span>
* <span data-ttu-id="0666b-744">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="0666b-744">Custom lifetime management</span></span>
* <span data-ttu-id="0666b-745">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="0666b-745">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="0666b-746">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="0666b-746">Convention-based registration</span></span>

<span data-ttu-id="0666b-747">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="0666b-747">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="0666b-748">Autofac</span><span class="sxs-lookup"><span data-stu-id="0666b-748">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="0666b-749">DryIoc</span><span class="sxs-lookup"><span data-stu-id="0666b-749">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="0666b-750">Grace</span><span class="sxs-lookup"><span data-stu-id="0666b-750">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="0666b-751">LightInject</span><span class="sxs-lookup"><span data-stu-id="0666b-751">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="0666b-752">Lamar</span><span class="sxs-lookup"><span data-stu-id="0666b-752">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="0666b-753">Stashbox</span><span class="sxs-lookup"><span data-stu-id="0666b-753">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="0666b-754">Unity</span><span class="sxs-lookup"><span data-stu-id="0666b-754">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="0666b-755">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="0666b-755">Thread safety</span></span>

<span data-ttu-id="0666b-756">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="0666b-756">Create thread-safe singleton services.</span></span> <span data-ttu-id="0666b-757">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="0666b-757">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="0666b-758">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="0666b-758">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="0666b-759">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="0666b-759">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="0666b-760">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="0666b-760">Recommendations</span></span>

* <span data-ttu-id="0666b-761">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="0666b-761">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="0666b-762">C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="0666b-762">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="0666b-763">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="0666b-763">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="0666b-764">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-764">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="0666b-765">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-765">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="0666b-766">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="0666b-766">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="0666b-767">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="0666b-767">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="0666b-768">Vermeiden Sie statischen Zugriff auf Dienste.</span><span class="sxs-lookup"><span data-stu-id="0666b-768">Avoid static access to services.</span></span> <span data-ttu-id="0666b-769">Vermeiden Sie statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle.</span><span class="sxs-lookup"><span data-stu-id="0666b-769">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="0666b-770">Vermeiden Sie die Verwendung des *Dienstlocatormusters* , da dieses [Steuerungsumkehrungsstrategien](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) vermischt.</span><span class="sxs-lookup"><span data-stu-id="0666b-770">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="0666b-771">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Abhängigkeitsinjektion verwenden können:</span><span class="sxs-lookup"><span data-stu-id="0666b-771">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="0666b-772">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="0666b-772">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="0666b-773">**Richtig** :</span><span class="sxs-lookup"><span data-stu-id="0666b-773">**Correct** :</span></span>

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

* <span data-ttu-id="0666b-774">Vermeiden Sie die Injektion von Factorys, die Abhängigkeiten zur Laufzeit mit <xref:System.IServiceProvider.GetService*> auflösen.</span><span class="sxs-lookup"><span data-stu-id="0666b-774">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="0666b-775">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="0666b-775">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="0666b-776">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="0666b-776">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="0666b-777">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="0666b-777">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="0666b-778">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="0666b-778">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="0666b-779">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="0666b-779">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0666b-780">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0666b-780">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="0666b-781">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0666b-781">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="0666b-782">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="0666b-782">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="0666b-783">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="0666b-783">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="0666b-784">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="0666b-784">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="0666b-785">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="0666b-785">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
