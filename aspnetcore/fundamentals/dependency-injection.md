---
title: Dependency Injection in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core Dependency Injection implementiert und wie Sie dieses Muster verwenden können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/21/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/dependency-injection
ms.openlocfilehash: 31db9aea9e0b7ed21cae2f87fbb9e2e649782697
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234464"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="cb7fc-103">Dependency Injection in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb7fc-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cb7fc-104">Von [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="cb7fc-105">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="cb7fc-106">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="cb7fc-107">Informationen zum Verwenden der Abhängigkeitsinjektion in anderen Apps als Web-Apps finden Sie unter [Abhängigkeitsinjektion in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-107">For information on using dependency injection in applications other than web apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="cb7fc-108">Weitere Informationen zur Dependency Injection für Optionen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-108">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="cb7fc-109">Dieses Thema enthält Informationen zur Dependency Injection in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-109">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="cb7fc-110">Die primäre Dokumentation zur Verwendung der Abhängigkeitsinjektion ist in [Abhängigkeitsinjektion in .NET](/dotnet/core/extensions/dependency-injection) enthalten.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-110">The primary documentation on using dependency injection is contained in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="cb7fc-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cb7fc-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="cb7fc-112">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="cb7fc-112">Overview of dependency injection</span></span>

<span data-ttu-id="cb7fc-113">Eine *Abhängigkeit* ist ein Objekt, von dem ein anderes Objekt abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-113">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="cb7fc-114">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="cb7fc-115">Eine Klasse kann eine Instanz der `MyDependency`-Klasse erstellen, um die `WriteMessage`-Methode zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-115">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="cb7fc-116">Im folgenden Beispiel ist die `MyDependency`-Klasse eine Abhängigkeit der `IndexModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-116">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="cb7fc-117">Die Klasse erstellt die `MyDependency`-Klasse und weist eine direkte Abhängigkeit von dieser auf.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-117">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="cb7fc-118">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-118">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="cb7fc-119">Die `IndexModel`-Klasse muss geändert werden, um `MyDependency` durch eine andere Implementierung zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-119">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="cb7fc-120">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese ebenfalls von der `IndexModel`-Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-120">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="cb7fc-121">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="cb7fc-122">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="cb7fc-123">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="cb7fc-124">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="cb7fc-125">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="cb7fc-126">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="cb7fc-127">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="cb7fc-128">Dienste werden üblicherweise in der `Startup.ConfigureServices`-Methode der App registriert.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-128">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="cb7fc-129">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="cb7fc-130">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="cb7fc-131">In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle die `WriteMessage`-Methode:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="cb7fc-132">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="cb7fc-133">Die Beispiel-App registriert den `IMyDependency`-Dienst mit dem konkreten Typ `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-133">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="cb7fc-134">Die <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>-Methode registriert den Dienst mit der Lebensdauer einer einzelnen Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-134">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="cb7fc-135">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-135">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="cb7fc-136">In der Beispiel-App wird der `IMyDependency`-Dienst angefordert und zum Aufrufen der `WriteMessage`-Methode verwendet:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-136">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="cb7fc-137">Bei Verwendung des Dependency-Injection-Musters geht der Controller wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-137">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="cb7fc-138">Er verwendet nicht den konkreten Typ `MyDependency`, nur die von diesem implementierte `IMyDependency`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-138">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="cb7fc-139">Dadurch kann die vom Controller verwendete Implementierung einfacher geändert werden, ohne den Controller selbst zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-139">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="cb7fc-140">Er erstellt keine Instanz von `MyDependency`. Diese wird vom DI-Container erstellt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-140">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="cb7fc-141">Die Implementierung der `IMyDependency`-Schnittstelle kann mithilfe der integrierten Protokollierungs-API verbessert werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-141">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="cb7fc-142">Die aktualisierte `ConfigureServices`-Methode registriert die neue `IMyDependency`-Implementierung:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-142">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="cb7fc-143">`MyDependency2` hängt von der <xref:Microsoft.Extensions.Logging.ILogger%601>-Schnittstelle ab, die im Konstruktor angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-143">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="cb7fc-144">`ILogger<TCategoryName>` ist ein [vom Framework bereitgestellter Dienst](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-144">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="cb7fc-145">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-145">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="cb7fc-146">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-146">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="cb7fc-147">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-147">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="cb7fc-148">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur* , *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-148">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="cb7fc-149">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch nicht mehr jeder [(generische) konstruierte Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) registriert werden muss:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-149">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="cb7fc-150">Im Dependency-Injection-Kontext ist ein Dienst:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-150">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="cb7fc-151">in der Regel ein Objekt, das einen Dienst für andere Objekte bereitstellt, z. B. den `IMyDependency`-Dienst.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-151">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="cb7fc-152">kein Webdienst, obwohl ein Dienst einen Webdienst verwenden kann</span><span class="sxs-lookup"><span data-stu-id="cb7fc-152">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="cb7fc-153">Das Framework bietet eine stabiles [Protokollierungssystem](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-153">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="cb7fc-154">Die in den vorherigen Beispielen veranschaulichten `IMyDependency`-Implementierungen wurden geschrieben, um die grundlegende Dependency Injection zu demonstrieren, nicht zum Implementieren der Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-154">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="cb7fc-155">Die meisten Apps müssen keine Protokollierungen schreiben.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-155">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="cb7fc-156">Der folgende Code veranschaulicht die Verwendung der Standardprotokollierung, bei der keine Dienste in `ConfigureServices` registriert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-156">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="cb7fc-157">Bei Verwendung des vorangehenden Codes muss `ConfigureServices` nicht aktualisiert werden, weil die [Protokollierung](xref:fundamentals/logging/index) vom Framework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-157">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="cb7fc-158">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="cb7fc-158">Services injected into Startup</span></span>

<span data-ttu-id="cb7fc-159">Dienste können in den `Startup`-Konstruktor und die `Startup.Configure`-Methode eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-159">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="cb7fc-160">Nur die folgenden Dienste können in den `Startup`-Konstruktor eingefügt werden, wenn der generische Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-160">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="cb7fc-161">Jeder Dienst, der mit dem DI-Container registriert wurde, kann in die `Startup.Configure`-Methode eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-161">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="cb7fc-162">Weitere Informationen finden Sie unter <xref:fundamentals/startup> und unter [Zugriffskonfiguration beim Start](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-162">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="cb7fc-163">Registrieren von Dienstgruppen mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="cb7fc-163">Register groups of services with extension methods</span></span>

<span data-ttu-id="cb7fc-164">Das ASP.NET Core-Framework verwendet eine Konvention zum Registrieren einer Gruppe verwandter Dienste.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-164">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="cb7fc-165">Die Konvention besteht darin, eine einzelne `Add{GROUP_NAME}`-Erweiterungsmethode zum Registrieren aller Dienste zu verwenden, die von einem Frameworkfeature benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-165">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="cb7fc-166">Beispielsweise registriert die Erweiterungsmethode <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> die Dienste, die für MVC-Controller erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-166">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="cb7fc-167">Der folgende Code wird von der :::no-loc(Razor)::: Pages-Vorlage auf Grundlage einzelner Benutzerkonten generiert. Er veranschaulicht, wie mit den Erweiterungsmethoden <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> und <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A> zusätzliche Dienste zum Container hinzugefügt werden können:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-167">The following code is generated by the :::no-loc(Razor)::: Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="cb7fc-168">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="cb7fc-168">Service lifetimes</span></span>

<span data-ttu-id="cb7fc-169">Weitere Informationen finden Sie unter [Dienstlebensdauer](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Abhängigkeitsinjektion in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-169">See [Service lifetimes](/dotnet/core/extensions/dependency-injection#service-lifetimes) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="cb7fc-170">Zum Verwenden bereichsbezogener Dienste in Middleware, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-170">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="cb7fc-171">Fügen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode der Middleware ein.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-171">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="cb7fc-172">Bei Verwendung der [Constructor Injection](xref:mvc/controllers/dependency-injection#constructor-injection) wird eine Runtimeausnahme ausgelöst, weil dem bereichsbezogenen Dienst das Verhalten eines Singletons aufgezwungen wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-172">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="cb7fc-173">Im Beispiel des Abschnitts [Lebensdauer und Registrierungsoptionen](#lifetime-and-registration-options) wird der `InvokeAsync`-Ansatz veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-173">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="cb7fc-174">Verwenden Sie [factorybezogene Middleware](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-174">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="cb7fc-175">Middleware, die mit diesem Ansatz registriert wurde, wird pro Clientanforderung (Verbindung) aktiviert, wodurch bereichsbezogene Dienste in die `InvokeAsync`-Methode der Middleware eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-175">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="cb7fc-176">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-176">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="cb7fc-177">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="cb7fc-177">Service registration methods</span></span>

<span data-ttu-id="cb7fc-178">Weitere Informationen finden Sie unter [Dienstregistrierungsmethoden](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Abhängigkeitsinjektion in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-178">See [Service registration methods](/dotnet/core/extensions/dependency-injection#service-registration-methods) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

 <span data-ttu-id="cb7fc-179">Es ist üblich, mehrere Implementierungen zu verwenden, [wenn Typen zu Testzecken simuliert werden](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-179">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="cb7fc-180">Das Registrieren eines Diensts mit nur einem Implementierungstyp entspricht dem Registrieren dieses Diensts mit demselben Implementierungs- und Diensttyp.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-180">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="cb7fc-181">Aus diesem Grund können nicht mehrere Implementierungen eines Diensts mithilfe von Methoden registriert werden, die keinen expliziten Diensttyp erwarten.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-181">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="cb7fc-182">Solche Methoden können mehrere *Instanzen* eines Diensts registrieren, die dann jedoch alle denselben *Implementierungstyp* aufweisen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-182">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="cb7fc-183">Alle oben genannten Dienstregistrierungsmethoden können zum Registrieren mehrerer Dienstinstanzen desselben Diensttyps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-183">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="cb7fc-184">Im folgenden Beispiel wird `AddSingleton` zweimal mit `IMyDependency` als Diensttyp aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-184">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="cb7fc-185">Mit dem zweiten Aufruf von `AddSingleton` wird der vorhandene Singleton überschrieben, wenn er als `IMyDependency` aufgelöst wurde. Wenn mehrere Dienste über `IEnumerable<IMyDependency>` aufgelöst werden, wird der neue Singleton hinzugefügt und der alte beibehalten.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-185">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="cb7fc-186">Dienste werden beim Auflösen mit `IEnumerable<{SERVICE}>` in der Reihenfolge angezeigt, in der sie registriert wurden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-186">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

## <a name="constructor-injection-behavior"></a><span data-ttu-id="cb7fc-187">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="cb7fc-187">Constructor injection behavior</span></span>

<span data-ttu-id="cb7fc-188">Weitere Informationen finden Sie unter [Konstruktorinjektionsverhalten](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Abhängigkeitsinjektion in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-188">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="cb7fc-189">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="cb7fc-189">Entity Framework contexts</span></span>

<span data-ttu-id="cb7fc-190">Entity Framework-Kontexte werden einem Dienstcontainer standardmäßig mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-190">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="cb7fc-191">Legen Sie die Lebensdauer mithilfe einer <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Überladung fest, um eine andere Lebensdauer zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-191">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="cb7fc-192">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer Lebensdauer verwenden, die kürzer als die Lebensdauer des Diensts ist.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-192">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="cb7fc-193">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="cb7fc-193">Lifetime and registration options</span></span>

<span data-ttu-id="cb7fc-194">In den folgenden Schnittstellen, die einen Task als Vorgang mit einem Bezeichner (`OperationId`) darstellen, wird der Unterschied zwischen Dienstlebensdauern und ihren Registrierungsoptionen veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-194">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="cb7fc-195">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-195">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="cb7fc-196">Die folgende `Operation`-Klasse implementiert alle vorangehenden Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-196">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="cb7fc-197">Der `Operation`-Konstruktor generiert einen eindeutigen Bezeichner (GUID) und speichert die letzten 4 Zeichen in der `OperationId`-Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-197">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="cb7fc-198">Die `Startup.ConfigureServices`-Methode erstellt mehrere Registrierungen der `Operation`-Klasse entsprechend der benannten Lebensdauern:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-198">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="cb7fc-199">Die Beispiel-App veranschaulicht die Objektlebensdauer sowohl in als auch zwischen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-199">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="cb7fc-200">`IndexModel` und die Middleware fordern jede Art von `IOperation`-Typ an und protokollieren `OperationId` für jeden Typ:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-200">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="cb7fc-201">Ähnlich wie `IndexModel` löst die Middleware dieselben Dienste auf:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-201">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="cb7fc-202">Bereichsbezogene Dienste müssen in der `InvokeAsync`-Methode aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-202">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="cb7fc-203">Die Protokollierungsausgabe zeigt Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-203">The logger output shows:</span></span>

* <span data-ttu-id="cb7fc-204">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-204">*Transient* objects are always different.</span></span> <span data-ttu-id="cb7fc-205">Der vorübergehende `OperationId`-Wert unterscheidet sich in `IndexModel` und der Middleware.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-205">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="cb7fc-206">*Bereichsbezogene* Objekte sind für jede Anforderung identisch, aber unterscheiden sich für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-206">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="cb7fc-207">*Singletonobjekte* sind für jede Anforderung identisch.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-207">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="cb7fc-208">Sie können "Logging:LogLevel:Microsoft:Error" in der Datei *appsettings.Development.json* festlegen, um die Protokollierungsausgabe zu reduzieren:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-208">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="cb7fc-209">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="cb7fc-209">Call services from main</span></span>

<span data-ttu-id="cb7fc-210">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-210">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="cb7fc-211">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-211">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="cb7fc-212">Im folgenden Beispiel wird der Zugriff auf den bereichsbezogenen `IMyDependency`-Dienst und der Aufruf dessen `WriteMessage`-Methode in `Program.Main` veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-212">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="cb7fc-213">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="cb7fc-213">Scope validation</span></span>

<span data-ttu-id="cb7fc-214">Weitere Informationen finden Sie unter [Konstruktorinjektionsverhalten](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Abhängigkeitsinjektion in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-214">See [Constructor injection behavior](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

<span data-ttu-id="cb7fc-215">Weitere Informationen finden Sie unter [Bereichsvalidierung](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-215">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="cb7fc-216">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="cb7fc-216">Request Services</span></span>

<span data-ttu-id="cb7fc-217">Die in einer ASP.NET Core-Anforderung verfügbaren Dienste werden über die Sammlung [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-217">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="cb7fc-218">Wenn Dienste innerhalb einer Anforderung angefordert werden, werden die Dienste und ihre Abhängigkeiten aus der `RequestServices`-Sammlung aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-218">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="cb7fc-219">Das Framework erstellt einen Bereich pro Anforderung, und `RequestServices` stellt den bereichsbezogenen Dienstanbieter zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-219">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="cb7fc-220">Alle bereichsbezogenen Dienste sind gültig, solange die Anforderung aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-220">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="cb7fc-221">Sie sollten das Anfordern von Abhängigkeiten als Konstruktorparameter zum Auflösen von Diensten aus der `RequestServices`-Sammlung bevorzugen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-221">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="cb7fc-222">Dies resultiert in Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-222">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="cb7fc-223">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="cb7fc-223">Design services for dependency injection</span></span>

<span data-ttu-id="cb7fc-224">Beachten Sie Folgendes beim Entwerfen von Diensten für Dependency Injection:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-224">When designing services for dependency injection:</span></span>

* <span data-ttu-id="cb7fc-225">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-225">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="cb7fc-226">Vermeiden Sie das Erstellen eines globalen Zustands, indem Sie Apps stattdessen zur Verwendung von Singletondiensten entwerfen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-226">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="cb7fc-227">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-227">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="cb7fc-228">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-228">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="cb7fc-229">Erstellen Sie kleine, gut gestaltete und einfach zu testende Dienste.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-229">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="cb7fc-230">Wenn eine Klasse viele eingefügte Abhängigkeiten aufweist, ist dies möglicherweise ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Prinzip der einzelnen Verantwortung (SRP, Single Responsibility Principle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) verstößt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-230">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="cb7fc-231">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Verantwortung in neue Klassen verschieben.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-231">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="cb7fc-232">Beachten Sie, dass der Fokus der :::no-loc(Razor)::: Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-232">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="cb7fc-233">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="cb7fc-233">Disposal of services</span></span>

<span data-ttu-id="cb7fc-234">Der Container ruft <xref:System.IDisposable.Dispose%2A> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-234">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="cb7fc-235">Dienste, die aus dem Container aufgelöst werden, sollten nie vom Entwickler gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-235">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="cb7fc-236">Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton automatisch vom Container verworfen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-236">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="cb7fc-237">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-237">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="cb7fc-238">In der Debuggingkonsole wird nach jeder Aktualisierung der Indexseite die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-238">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="cb7fc-239">Nicht vom Dienstcontainer erstellte Dienste</span><span class="sxs-lookup"><span data-stu-id="cb7fc-239">Services not created by the service container</span></span>

<span data-ttu-id="cb7fc-240">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-240">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="cb7fc-241">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-241">In the preceding code:</span></span>

* <span data-ttu-id="cb7fc-242">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-242">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="cb7fc-243">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-243">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="cb7fc-244">Der Entwickler ist für das Löschen der Dienste verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-244">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="cb7fc-245">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="cb7fc-245">IDisposable guidance for Transient and shared instances</span></span>

<span data-ttu-id="cb7fc-246">Weitere Informationen finden Sie unter [IDisposable-Leitfaden für vorübergehende und freigegebene Instanzen](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Abhängigkeitsinjektion in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-246">See [IDisposable guidance for Transient and shared instance](/dotnet/core/extensions/dependency-injection-guidelines#idisposable-guidance-for-transient-and-shared-instances) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="cb7fc-247">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="cb7fc-247">Default service container replacement</span></span>

<span data-ttu-id="cb7fc-248">Weitere Informationen finden Sie unter [Ersetzen von Standarddienstcontainern](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Abhängigkeitsinjektion in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-248">See [Default service container replacement](/dotnet/core/extensions/dependency-injection-guidelines#default-service-container-replacement) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

## <a name="recommendations"></a><span data-ttu-id="cb7fc-249">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="cb7fc-249">Recommendations</span></span>

<span data-ttu-id="cb7fc-250">Weitere Informationen finden Sie unter [Empfehlungen](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Abhängigkeitsinjektion in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-250">See [Recommendations](/dotnet/core/extensions/dependency-injection-guidelines#recommendations) in [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection)</span></span>

* <span data-ttu-id="cb7fc-251">Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-251">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="cb7fc-252">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService%2A> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-252">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="cb7fc-253">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="cb7fc-253">**Incorrect:**</span></span>

    ![Falscher Code](dependency-injection/_static/bad.png)

  <span data-ttu-id="cb7fc-255">**Richtig** :</span><span class="sxs-lookup"><span data-stu-id="cb7fc-255">**Correct** :</span></span>

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
* <span data-ttu-id="cb7fc-256">Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-256">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="cb7fc-257">Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-257">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="cb7fc-258">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-258">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="cb7fc-259">Vermeiden Sie Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-259">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="cb7fc-260">`BuildServiceProvider` wird in der Regel aufgerufen, wenn der Entwickler einen Dienst in `ConfigureServices` auflösen möchte.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-260">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="cb7fc-261">Berücksichtigen Sie beispielsweise den Fall, wenn `LoginPath` aus der Konfiguration geladen wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-261">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="cb7fc-262">Vermeiden Sie den folgenden Ansatz:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-262">Avoid the following approach:</span></span>

  ![Ungültiger Code beim Aufruf von BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="cb7fc-264">Wenn Sie auf die grüne Wellenlinie unter `services.BuildServiceProvider` auf der vorherigen Abbildung klicken würden, würde folgende ASP0000-Warnung angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-264">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="cb7fc-265">ASP0000: Der Aufruf von BuildServiceProvider aus dem Anwendungscode führt dazu, dass eine zusätzliche Kopie der Singletondienste erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-265">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="cb7fc-266">Verwenden Sie Alternativen wie Dependency-Injection-Dienste als Parameter für Configure.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-266">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="cb7fc-267">Durch den Aufruf von `BuildServiceProvider` wird ein zweiter Container erstellt, der fragmentierte Singletons und Verweise auf Objektgraphen für mehrere Container verursachen kann.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-267">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="cb7fc-268">Eine korrekte Methode zum Abrufen von `LoginPath` besteht darin, die integrierte Unterstützung von DI des Optionsmusters zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-268">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="cb7fc-269">Löschbare temporäre Dienste werden vom Container für die Löschung erfasst.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-269">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="cb7fc-270">Dadurch kann es zu Arbeitsspeicherverlusten kommen, wenn diese vom obersten Container aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-270">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="cb7fc-271">Aktivieren Sie die Bereichsüberprüfung, um sicherzustellen, dass die App keine Singletons aufweist, die bereichsbezogene Dienste erfassen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-271">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="cb7fc-272">Weitere Informationen finden Sie unter [Bereichsvalidierung](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-272">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="cb7fc-273">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-273">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="cb7fc-274">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-274">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="cb7fc-275">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-275">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="cb7fc-276">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-276">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="cb7fc-277">Empfohlene Muster für Mehrinstanzenfähigkeit in der Dependency Injection (DI)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-277">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="cb7fc-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) ist ein Anwendungsframework zum Erstellen modularer Anwendung mit mehreren Mandanten in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-278">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="cb7fc-279">Weitere Informationen finden Sie in der [Orchard Core-Dokumentation](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-279">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="cb7fc-280">Beispiele zum Erstellen modularer Apps und Apps mit mehreren Mandanten nur mit dem Orchard Core-Framework und ohne den CMS-spezifischen Features finden Sie in den [Orchard Core-Beispielen](https://github.com/OrchardCMS/OrchardCore.Samples).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-280">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="cb7fc-281">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="cb7fc-281">Framework-provided services</span></span>

<span data-ttu-id="cb7fc-282">Die `Startup.ConfigureServices`-Methode registriert Dienste, die von der App verwendet werden, einschließlich Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-282">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="cb7fc-283">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-283">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="cb7fc-284">Für Apps, die auf ASP.NET Core-Vorlagen basieren, registriert das Framework mehr als 250 Dienste.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-284">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="cb7fc-285">In der folgenden Tabelle finden Sie eine kleine Stichprobe der vom Framework registrierten Dienste:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-285">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="cb7fc-286">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="cb7fc-286">Service Type</span></span>                                                                                    | <span data-ttu-id="cb7fc-287">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="cb7fc-287">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="cb7fc-288">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-288">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="cb7fc-289">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-289">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="cb7fc-290">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-290">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="cb7fc-291">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-291">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="cb7fc-292">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-292">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="cb7fc-293">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-293">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="cb7fc-294">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-294">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="cb7fc-295">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-295">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="cb7fc-296">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-296">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="cb7fc-297">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-297">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="cb7fc-298">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-298">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="cb7fc-299">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-299">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="cb7fc-300">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-300">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="cb7fc-301">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-301">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="cb7fc-302">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cb7fc-302">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="cb7fc-303">NDC-Konferenzmuster für die DI-App-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="cb7fc-303">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="cb7fc-304">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb7fc-304">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="cb7fc-305">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-305">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="cb7fc-306">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="cb7fc-306">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="cb7fc-307">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-307">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="cb7fc-308">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="cb7fc-308">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cb7fc-309">Von [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-309">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="cb7fc-310">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-310">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="cb7fc-311">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-311">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="cb7fc-312">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cb7fc-312">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="cb7fc-313">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="cb7fc-313">Overview of dependency injection</span></span>

<span data-ttu-id="cb7fc-314">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-314">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="cb7fc-315">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-315">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="cb7fc-316">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-316">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="cb7fc-317">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-317">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="cb7fc-318">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-318">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="cb7fc-319">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-319">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="cb7fc-320">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-320">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="cb7fc-321">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-321">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="cb7fc-322">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-322">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="cb7fc-323">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-323">This implementation is difficult to unit test.</span></span> <span data-ttu-id="cb7fc-324">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-324">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="cb7fc-325">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-325">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="cb7fc-326">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-326">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="cb7fc-327">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-327">Registration of the dependency in a service container.</span></span> <span data-ttu-id="cb7fc-328">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-328">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="cb7fc-329">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-329">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="cb7fc-330">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-330">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="cb7fc-331">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-331">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="cb7fc-332">In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-332">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="cb7fc-333">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-333">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="cb7fc-334">`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-334">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="cb7fc-335">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-335">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="cb7fc-336">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-336">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="cb7fc-337">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-337">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="cb7fc-338">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur* , *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-338">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree* , *dependency graph* , or *object graph*.</span></span>

<span data-ttu-id="cb7fc-339">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-339">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="cb7fc-340">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-340">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cb7fc-341">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-341">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="cb7fc-342">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-342">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="cb7fc-343">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-343">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="cb7fc-344">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-344">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="cb7fc-345">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-345">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="cb7fc-346">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-346">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="cb7fc-347">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste :::no-loc(Razor)::: Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-347">For example, `services.AddMvc()` adds the services :::no-loc(Razor)::: Pages and MVC require.</span></span> <span data-ttu-id="cb7fc-348">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-348">We recommended that apps follow this convention.</span></span> <span data-ttu-id="cb7fc-349">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-349">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="cb7fc-350">Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-350">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="cb7fc-351">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-351">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="cb7fc-352">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-352">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="cb7fc-353">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-353">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="cb7fc-354">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="cb7fc-354">Services injected into Startup</span></span>

<span data-ttu-id="cb7fc-355">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-355">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="cb7fc-356">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-356">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="cb7fc-357">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-357">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="cb7fc-358">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="cb7fc-358">Framework-provided services</span></span>

<span data-ttu-id="cb7fc-359">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-359">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="cb7fc-360">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-360">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="cb7fc-361">Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-361">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="cb7fc-362">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-362">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="cb7fc-363">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="cb7fc-363">Service Type</span></span> | <span data-ttu-id="cb7fc-364">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="cb7fc-364">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="cb7fc-365">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-365">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="cb7fc-366">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-366">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="cb7fc-367">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-367">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="cb7fc-368">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-368">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="cb7fc-369">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-369">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="cb7fc-370">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-370">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="cb7fc-371">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-371">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="cb7fc-372">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-372">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="cb7fc-373">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-373">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="cb7fc-374">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-374">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="cb7fc-375">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-375">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="cb7fc-376">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-376">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="cb7fc-377">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-377">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="cb7fc-378">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-378">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="cb7fc-379">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="cb7fc-379">Register additional services with extension methods</span></span>

<span data-ttu-id="cb7fc-380">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-380">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="cb7fc-381">Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*> zusätzliche Dienste zum Container hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-381">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::Core*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    ...
}
```

<span data-ttu-id="cb7fc-382">Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-382">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="cb7fc-383">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="cb7fc-383">Service lifetimes</span></span>

<span data-ttu-id="cb7fc-384">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-384">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="cb7fc-385">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-385">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="cb7fc-386">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-386">Transient</span></span>

<span data-ttu-id="cb7fc-387">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-387">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="cb7fc-388">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-388">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="cb7fc-389">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-389">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="cb7fc-390">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="cb7fc-390">Scoped</span></span>

<span data-ttu-id="cb7fc-391">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-391">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="cb7fc-392">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-392">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="cb7fc-393">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-393">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="cb7fc-394">Fügen Sie ihn nicht über [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-394">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="cb7fc-395">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-395">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="cb7fc-396">Singleton</span><span class="sxs-lookup"><span data-stu-id="cb7fc-396">Singleton</span></span>

<span data-ttu-id="cb7fc-397">Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-397">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="cb7fc-398">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-398">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="cb7fc-399">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-399">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="cb7fc-400">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-400">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="cb7fc-401">In Apps, die Anforderungen verarbeiten, werden Singleton-Dienste verworfen, wenn der <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren der App verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-401">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="cb7fc-402">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-402">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="cb7fc-403">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-403">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="cb7fc-404">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="cb7fc-404">Service registration methods</span></span>

<span data-ttu-id="cb7fc-405">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-405">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="cb7fc-406">Methode</span><span class="sxs-lookup"><span data-stu-id="cb7fc-406">Method</span></span> | <span data-ttu-id="cb7fc-407">Automatische</span><span class="sxs-lookup"><span data-stu-id="cb7fc-407">Automatic</span></span><br><span data-ttu-id="cb7fc-408">Objekt</span><span class="sxs-lookup"><span data-stu-id="cb7fc-408">object</span></span><br><span data-ttu-id="cb7fc-409">bereinigung</span><span class="sxs-lookup"><span data-stu-id="cb7fc-409">disposal</span></span> | <span data-ttu-id="cb7fc-410">Mehrere</span><span class="sxs-lookup"><span data-stu-id="cb7fc-410">Multiple</span></span><br><span data-ttu-id="cb7fc-411">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="cb7fc-411">implementations</span></span> | <span data-ttu-id="cb7fc-412">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="cb7fc-412">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="cb7fc-413">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-413">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="cb7fc-414">Ja</span><span class="sxs-lookup"><span data-stu-id="cb7fc-414">Yes</span></span> | <span data-ttu-id="cb7fc-415">Ja</span><span class="sxs-lookup"><span data-stu-id="cb7fc-415">Yes</span></span> | <span data-ttu-id="cb7fc-416">Nein</span><span class="sxs-lookup"><span data-stu-id="cb7fc-416">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="cb7fc-417">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-417">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="cb7fc-418">Ja</span><span class="sxs-lookup"><span data-stu-id="cb7fc-418">Yes</span></span> | <span data-ttu-id="cb7fc-419">Ja</span><span class="sxs-lookup"><span data-stu-id="cb7fc-419">Yes</span></span> | <span data-ttu-id="cb7fc-420">Ja</span><span class="sxs-lookup"><span data-stu-id="cb7fc-420">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="cb7fc-421">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-421">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="cb7fc-422">Ja</span><span class="sxs-lookup"><span data-stu-id="cb7fc-422">Yes</span></span> | <span data-ttu-id="cb7fc-423">Nein</span><span class="sxs-lookup"><span data-stu-id="cb7fc-423">No</span></span> | <span data-ttu-id="cb7fc-424">Nein</span><span class="sxs-lookup"><span data-stu-id="cb7fc-424">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="cb7fc-425">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-425">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="cb7fc-426">Nein</span><span class="sxs-lookup"><span data-stu-id="cb7fc-426">No</span></span> | <span data-ttu-id="cb7fc-427">Ja</span><span class="sxs-lookup"><span data-stu-id="cb7fc-427">Yes</span></span> | <span data-ttu-id="cb7fc-428">Ja</span><span class="sxs-lookup"><span data-stu-id="cb7fc-428">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="cb7fc-429">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-429">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="cb7fc-430">Nein</span><span class="sxs-lookup"><span data-stu-id="cb7fc-430">No</span></span> | <span data-ttu-id="cb7fc-431">Nein</span><span class="sxs-lookup"><span data-stu-id="cb7fc-431">No</span></span> | <span data-ttu-id="cb7fc-432">Ja</span><span class="sxs-lookup"><span data-stu-id="cb7fc-432">Yes</span></span> |

<span data-ttu-id="cb7fc-433">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-433">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="cb7fc-434">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-434">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="cb7fc-435">Das Registrieren eines Diensts mit nur einem Implementierungstyp entspricht dem Registrieren dieses Diensts mit demselben Implementierungs- und Diensttyp.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-435">Registering a service with only an implementation type is equivalent to registering that service with the same implementation and service type.</span></span> <span data-ttu-id="cb7fc-436">Aus diesem Grund können nicht mehrere Implementierungen eines Diensts mithilfe von Methoden registriert werden, die keinen expliziten Diensttyp erwarten.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-436">This is why multiple implementations of a service cannot be registered using the methods that don't take an explicit service type.</span></span> <span data-ttu-id="cb7fc-437">Solche Methoden können mehrere *Instanzen* eines Diensts registrieren, die dann jedoch alle denselben *Implementierungstyp* aufweisen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-437">These methods can register multiple *instances* of a service, but they will all have the same *implementation* type.</span></span>

<span data-ttu-id="cb7fc-438">Alle oben genannten Dienstregistrierungsmethoden können zum Registrieren mehrerer Dienstinstanzen desselben Diensttyps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-438">Any of the above service registration methods can be used to register multiple service instances of the same service type.</span></span> <span data-ttu-id="cb7fc-439">Im folgenden Beispiel wird `AddSingleton` zweimal mit `IMyDependency` als Diensttyp aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-439">In the following example, `AddSingleton` is called twice with `IMyDependency` as the service type.</span></span> <span data-ttu-id="cb7fc-440">Mit dem zweiten Aufruf von `AddSingleton` wird der vorhandene Singleton überschrieben, wenn er als `IMyDependency` aufgelöst wurde. Wenn mehrere Dienste über `IEnumerable<IMyDependency>` aufgelöst werden, wird der neue Singleton hinzugefügt und der alte beibehalten.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-440">The second call to `AddSingleton` overrides the previous one when resolved as `IMyDependency` and adds to the previous one when multiple services are resolved via `IEnumerable<IMyDependency>`.</span></span> <span data-ttu-id="cb7fc-441">Dienste werden beim Auflösen mit `IEnumerable<{SERVICE}>` in der Reihenfolge angezeigt, in der sie registriert wurden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-441">Services appear in the order they were registered when resolved via `IEnumerable<{SERVICE}>`.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
services.AddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
       IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is DifferentDependency);

        var dependencyArray = myDependencies.ToArray();
        Trace.Assert(dependencyArray[0] is MyDependency);
        Trace.Assert(dependencyArray[1] is DifferentDependency);
    }
}
```

<span data-ttu-id="cb7fc-442">Das Framework stellt außerdem `TryAdd{LIFETIME}`-Erweiterungsmethoden bereit, die den Dienst nur registrieren, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-442">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="cb7fc-443">Im folgenden Beispiel registriert der `AddSingleton`-Aufruf `MyDependency` als Implementierung für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-443">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="cb7fc-444">Der Aufruf von `TryAddSingleton` hat keine Auswirkung, da `IMyDependency` bereits eine registrierte Implementierung aufweist.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-444">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation.</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();

public class MyService
{
    public MyService(IMyDependency myDependency, 
        IEnumerable<IMyDependency> myDependencies)
    {
        Trace.Assert(myDependency is MyDependency);
        Trace.Assert(myDependencies.Single() is MyDependency);
    }
}
```

<span data-ttu-id="cb7fc-445">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-445">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="cb7fc-446">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-446">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="cb7fc-447">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-447">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="cb7fc-448">Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-448">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="cb7fc-449">Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-449">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="cb7fc-450">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-450">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="cb7fc-451">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-451">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="cb7fc-452">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-452">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="cb7fc-453">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="cb7fc-453">Constructor injection behavior</span></span>

<span data-ttu-id="cb7fc-454">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-454">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="cb7fc-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Lässt die Erstellung von Objekten ohne Dienstregistrierung im Dependency-Injection-Container zu</span><span class="sxs-lookup"><span data-stu-id="cb7fc-455"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="cb7fc-456">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-456">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="cb7fc-457">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-457">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="cb7fc-458">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-458">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="cb7fc-459">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-459">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="cb7fc-460">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-460">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="cb7fc-461">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="cb7fc-461">Entity Framework contexts</span></span>

<span data-ttu-id="cb7fc-462">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-462">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="cb7fc-463">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-463">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="cb7fc-464">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-464">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="cb7fc-465">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="cb7fc-465">Lifetime and registration options</span></span>

<span data-ttu-id="cb7fc-466">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-466">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="cb7fc-467">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-467">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="cb7fc-468">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-468">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="cb7fc-469">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-469">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="cb7fc-470">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-470">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="cb7fc-471">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-471">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="cb7fc-472">Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-472">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="cb7fc-473">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-473">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="cb7fc-474">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-474">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="cb7fc-475">Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-475">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="cb7fc-476">Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-476">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="cb7fc-477">Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-477">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="cb7fc-478">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-478">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="cb7fc-479">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-479">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="cb7fc-480">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-480">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="cb7fc-481">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-481">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="cb7fc-482">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-482">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="cb7fc-483">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-483">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="cb7fc-484">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-484">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="cb7fc-485">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="cb7fc-485">**First request:**</span></span>

<span data-ttu-id="cb7fc-486">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-486">Controller operations:</span></span>

<span data-ttu-id="cb7fc-487">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="cb7fc-487">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="cb7fc-488">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="cb7fc-488">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="cb7fc-489">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cb7fc-489">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cb7fc-490">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cb7fc-490">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cb7fc-491">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-491">`OperationService` operations:</span></span>

<span data-ttu-id="cb7fc-492">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="cb7fc-492">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="cb7fc-493">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="cb7fc-493">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="cb7fc-494">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cb7fc-494">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cb7fc-495">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cb7fc-495">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cb7fc-496">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="cb7fc-496">**Second request:**</span></span>

<span data-ttu-id="cb7fc-497">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-497">Controller operations:</span></span>

<span data-ttu-id="cb7fc-498">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="cb7fc-498">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="cb7fc-499">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="cb7fc-499">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="cb7fc-500">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cb7fc-500">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cb7fc-501">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cb7fc-501">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cb7fc-502">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-502">`OperationService` operations:</span></span>

<span data-ttu-id="cb7fc-503">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="cb7fc-503">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="cb7fc-504">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="cb7fc-504">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="cb7fc-505">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="cb7fc-505">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="cb7fc-506">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="cb7fc-506">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="cb7fc-507">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-507">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="cb7fc-508">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-508">*Transient* objects are always different.</span></span> <span data-ttu-id="cb7fc-509">Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-509">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="cb7fc-510">Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-510">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="cb7fc-511">*Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-511">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="cb7fc-512">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-512">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="cb7fc-513">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="cb7fc-513">Call services from main</span></span>

<span data-ttu-id="cb7fc-514">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-514">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="cb7fc-515">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-515">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="cb7fc-516">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-516">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="cb7fc-517">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="cb7fc-517">Scope validation</span></span>

<span data-ttu-id="cb7fc-518">Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-518">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="cb7fc-519">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="cb7fc-519">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="cb7fc-520">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="cb7fc-520">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="cb7fc-521">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-521">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="cb7fc-522">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-522">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="cb7fc-523">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-523">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="cb7fc-524">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-524">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="cb7fc-525">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-525">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="cb7fc-526">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-526">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="cb7fc-527">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="cb7fc-527">Request Services</span></span>

<span data-ttu-id="cb7fc-528">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-528">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="cb7fc-529">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-529">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="cb7fc-530">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-530">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="cb7fc-531">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-531">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="cb7fc-532">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-532">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="cb7fc-533">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-533">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="cb7fc-534">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-534">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="cb7fc-535">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="cb7fc-535">Design services for dependency injection</span></span>

<span data-ttu-id="cb7fc-536">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-536">Best practices are to:</span></span>

* <span data-ttu-id="cb7fc-537">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-537">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="cb7fc-538">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-538">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="cb7fc-539">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-539">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="cb7fc-540">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-540">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="cb7fc-541">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-541">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="cb7fc-542">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-542">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="cb7fc-543">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-543">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="cb7fc-544">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-544">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="cb7fc-545">Beachten Sie, dass der Fokus der :::no-loc(Razor)::: Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-545">Keep in mind that :::no-loc(Razor)::: Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="cb7fc-546">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-546">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="cb7fc-547">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="cb7fc-547">Disposal of services</span></span>

<span data-ttu-id="cb7fc-548">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-548">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="cb7fc-549">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-549">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="cb7fc-550">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-550">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="cb7fc-551">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-551">In the following example:</span></span>

* <span data-ttu-id="cb7fc-552">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-552">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="cb7fc-553">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-553">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="cb7fc-554">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-554">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="cb7fc-555">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-555">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="cb7fc-556">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="cb7fc-556">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="cb7fc-557">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="cb7fc-557">Transient, limited lifetime</span></span>

<span data-ttu-id="cb7fc-558">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="cb7fc-558">**Scenario**</span></span>

<span data-ttu-id="cb7fc-559">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-559">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="cb7fc-560">Die-Instanz wird im Stammbereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-560">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="cb7fc-561">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-561">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="cb7fc-562">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="cb7fc-562">**Solution**</span></span>

<span data-ttu-id="cb7fc-563">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-563">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="cb7fc-564">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-564">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="cb7fc-565">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-565">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="cb7fc-566">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-566">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="cb7fc-567">Sie kann <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> verwenden, um eine Instanz außerhalb des Containers zu instanziieren und dabei den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-567">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="cb7fc-568">Freigegebene Instanz, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="cb7fc-568">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="cb7fc-569">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="cb7fc-569">**Scenario**</span></span>

<span data-ttu-id="cb7fc-570">Für die App ist eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste erforderlich, die <xref:System.IDisposable> sollte jedoch eine begrenzte Lebensdauer haben.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-570">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="cb7fc-571">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="cb7fc-571">**Solution**</span></span>

<span data-ttu-id="cb7fc-572">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-572">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="cb7fc-573">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> für den Start und zum Erstellen einer neuen <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-573">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="cb7fc-574">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-574">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="cb7fc-575">Löschen Sie den Bereich, wenn die Lebensdauer beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-575">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="cb7fc-576">Allgemeine Richtlinien</span><span class="sxs-lookup"><span data-stu-id="cb7fc-576">General Guidelines</span></span>

* <span data-ttu-id="cb7fc-577">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einem vorübergehenden Gültigkeitsbereich.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-577">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="cb7fc-578">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-578">Use the factory pattern instead.</span></span>
* <span data-ttu-id="cb7fc-579">Lösen Sie keine vorübergehenden oder bereichsbezogenen <xref:System.IDisposable>-Instanzen im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-579">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="cb7fc-580">Die einzige allgemeine Ausnahme gilt, wenn die App die <xref:System.IServiceProvider>-Instanz erstellt bzw. erneut erstellt und freigibt, was kein ideales Muster ist.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-580">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="cb7fc-581">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-581">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="cb7fc-582">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-582">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="cb7fc-583">Bereiche sollten verwendet werden, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-583">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="cb7fc-584">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-584">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="cb7fc-585">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="cb7fc-585">Default service container replacement</span></span>

<span data-ttu-id="cb7fc-586">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-586">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="cb7fc-587">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-587">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="cb7fc-588">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="cb7fc-588">Property injection</span></span>
* <span data-ttu-id="cb7fc-589">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="cb7fc-589">Injection based on name</span></span>
* <span data-ttu-id="cb7fc-590">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="cb7fc-590">Child containers</span></span>
* <span data-ttu-id="cb7fc-591">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="cb7fc-591">Custom lifetime management</span></span>
* <span data-ttu-id="cb7fc-592">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="cb7fc-592">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="cb7fc-593">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="cb7fc-593">Convention-based registration</span></span>

<span data-ttu-id="cb7fc-594">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-594">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="cb7fc-595">Autofac</span><span class="sxs-lookup"><span data-stu-id="cb7fc-595">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="cb7fc-596">DryIoc</span><span class="sxs-lookup"><span data-stu-id="cb7fc-596">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="cb7fc-597">Grace</span><span class="sxs-lookup"><span data-stu-id="cb7fc-597">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="cb7fc-598">LightInject</span><span class="sxs-lookup"><span data-stu-id="cb7fc-598">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="cb7fc-599">Lamar</span><span class="sxs-lookup"><span data-stu-id="cb7fc-599">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="cb7fc-600">Stashbox</span><span class="sxs-lookup"><span data-stu-id="cb7fc-600">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="cb7fc-601">Unity</span><span class="sxs-lookup"><span data-stu-id="cb7fc-601">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="cb7fc-602">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="cb7fc-602">Thread safety</span></span>

<span data-ttu-id="cb7fc-603">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-603">Create thread-safe singleton services.</span></span> <span data-ttu-id="cb7fc-604">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-604">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="cb7fc-605">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-605">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="cb7fc-606">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-606">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="cb7fc-607">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="cb7fc-607">Recommendations</span></span>

* <span data-ttu-id="cb7fc-608">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-608">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="cb7fc-609">C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-609">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="cb7fc-610">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-610">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="cb7fc-611">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-611">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="cb7fc-612">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-612">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="cb7fc-613">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-613">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="cb7fc-614">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-614">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="cb7fc-615">Vermeiden Sie statischen Zugriff auf Dienste.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-615">Avoid static access to services.</span></span> <span data-ttu-id="cb7fc-616">Vermeiden Sie statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-616">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="cb7fc-617">Vermeiden Sie die Verwendung des *Dienstlocatormusters* , da dieses [Steuerungsumkehrungsstrategien](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) vermischt.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-617">Avoid using the *service locator pattern* , which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="cb7fc-618">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Abhängigkeitsinjektion verwenden können:</span><span class="sxs-lookup"><span data-stu-id="cb7fc-618">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="cb7fc-619">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="cb7fc-619">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="cb7fc-620">**Richtig** :</span><span class="sxs-lookup"><span data-stu-id="cb7fc-620">**Correct** :</span></span>

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

* <span data-ttu-id="cb7fc-621">Vermeiden Sie die Injektion von Factorys, die Abhängigkeiten zur Laufzeit mit <xref:System.IServiceProvider.GetService*> auflösen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-621">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="cb7fc-622">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="cb7fc-622">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="cb7fc-623">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-623">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="cb7fc-624">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-624">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="cb7fc-625">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-625">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="cb7fc-626">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="cb7fc-626">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cb7fc-627">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cb7fc-627">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="cb7fc-628">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cb7fc-628">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="cb7fc-629">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-629">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="cb7fc-630">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="cb7fc-630">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="cb7fc-631">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="cb7fc-631">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="cb7fc-632">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="cb7fc-632">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
