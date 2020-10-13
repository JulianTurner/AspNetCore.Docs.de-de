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
ms.openlocfilehash: 99e0109ea4c2526e9f91a8a4df23c4557e9be83a
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762307"
---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="bba0b-103">Dependency Injection in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bba0b-103">Dependency injection in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bba0b-104">Von [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="bba0b-104">By [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="bba0b-105">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-105">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="bba0b-106">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bba0b-106">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="bba0b-107">Weitere Informationen zur Dependency Injection für Optionen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="bba0b-107">For more information on dependency injection of options, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="bba0b-108">Dieses Thema enthält Informationen zur Dependency Injection in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bba0b-108">This topic provides information on dependency injection in ASP.NET Core.</span></span> <span data-ttu-id="bba0b-109">Informationen zur Verwendung der Dependency Injection in Konsolen-Apps finden Sie unter [Dependency Injection in .NET](/dotnet/core/extensions/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="bba0b-109">For information on using dependency injection  in console apps, see [Dependency injection in .NET](/dotnet/core/extensions/dependency-injection).</span></span>

<span data-ttu-id="bba0b-110">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bba0b-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="bba0b-111">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="bba0b-111">Overview of dependency injection</span></span>

<span data-ttu-id="bba0b-112">Eine *Abhängigkeit* ist ein Objekt, von dem ein anderes Objekt abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-112">A *dependency* is an object that another object depends on.</span></span> <span data-ttu-id="bba0b-113">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen abhängig sind:</span><span class="sxs-lookup"><span data-stu-id="bba0b-113">Examine the following `MyDependency` class with a `WriteMessage` method that other classes depend on:</span></span>

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

<span data-ttu-id="bba0b-114">Eine Klasse kann eine Instanz der `MyDependency`-Klasse erstellen, um die `WriteMessage`-Methode zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-114">A class can create an instance of the `MyDependency` class to make use of its `WriteMessage` method.</span></span> <span data-ttu-id="bba0b-115">Im folgenden Beispiel ist die `MyDependency`-Klasse eine Abhängigkeit der `IndexModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="bba0b-115">In the following example, the `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="bba0b-116">Die Klasse erstellt die `MyDependency`-Klasse und weist eine direkte Abhängigkeit von dieser auf.</span><span class="sxs-lookup"><span data-stu-id="bba0b-116">The class creates and directly depends on the `MyDependency` class.</span></span> <span data-ttu-id="bba0b-117">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-117">Code dependencies, such as in the previous example, are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="bba0b-118">Die `IndexModel`-Klasse muss geändert werden, um `MyDependency` durch eine andere Implementierung zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-118">To replace `MyDependency` with a different implementation, the `IndexModel` class must be modified.</span></span>
* <span data-ttu-id="bba0b-119">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese ebenfalls von der `IndexModel`-Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-119">If `MyDependency` has dependencies, they must also be configured by the `IndexModel` class.</span></span> <span data-ttu-id="bba0b-120">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-120">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="bba0b-121">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-121">This implementation is difficult to unit test.</span></span> <span data-ttu-id="bba0b-122">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-122">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="bba0b-123">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="bba0b-123">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="bba0b-124">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="bba0b-124">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="bba0b-125">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="bba0b-125">Registration of the dependency in a service container.</span></span> <span data-ttu-id="bba0b-126">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="bba0b-126">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="bba0b-127">Dienste werden üblicherweise in der `Startup.ConfigureServices`-Methode der App registriert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-127">Services are typically registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="bba0b-128">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-128">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="bba0b-129">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-129">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="bba0b-130">In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle die `WriteMessage`-Methode:</span><span class="sxs-lookup"><span data-stu-id="bba0b-130">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="bba0b-131">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="bba0b-131">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="bba0b-132">Die Beispiel-App registriert den `IMyDependency`-Dienst mit dem konkreten Typ `MyDependency`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-132">The sample app registers the `IMyDependency` service with the concrete type `MyDependency`.</span></span> <span data-ttu-id="bba0b-133">Die <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>-Methode registriert den Dienst mit der Lebensdauer einer einzelnen Anforderung.</span><span class="sxs-lookup"><span data-stu-id="bba0b-133">The <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> method registers the service with a scoped lifetime, the lifetime of a single request.</span></span> <span data-ttu-id="bba0b-134">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-134">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

<span data-ttu-id="bba0b-135">In der Beispiel-App wird der `IMyDependency`-Dienst angefordert und zum Aufrufen der `WriteMessage`-Methode verwendet:</span><span class="sxs-lookup"><span data-stu-id="bba0b-135">In the sample app, the `IMyDependency` service is requested and used to call the `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

<span data-ttu-id="bba0b-136">Bei Verwendung des Dependency-Injection-Musters geht der Controller wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="bba0b-136">By using the DI pattern, the controller:</span></span>

* <span data-ttu-id="bba0b-137">Er verwendet nicht den konkreten Typ `MyDependency`, nur die von diesem implementierte `IMyDependency`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="bba0b-137">Doesn't use the concrete type `MyDependency`, only the `IMyDependency` interface it implements.</span></span> <span data-ttu-id="bba0b-138">Dadurch kann die vom Controller verwendete Implementierung einfacher geändert werden, ohne den Controller selbst zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="bba0b-138">That makes it easy to change the implementation that the controller uses without modifying the controller.</span></span>
* <span data-ttu-id="bba0b-139">Er erstellt keine Instanz von `MyDependency`. Diese wird vom DI-Container erstellt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-139">Doesn't create an instance of `MyDependency`, it's created by the DI container.</span></span>

<span data-ttu-id="bba0b-140">Die Implementierung der `IMyDependency`-Schnittstelle kann mithilfe der integrierten Protokollierungs-API verbessert werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-140">The implementation of the `IMyDependency` interface can be improved by using the built-in logging API:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

<span data-ttu-id="bba0b-141">Die aktualisierte `ConfigureServices`-Methode registriert die neue `IMyDependency`-Implementierung:</span><span class="sxs-lookup"><span data-stu-id="bba0b-141">The updated `ConfigureServices` method registers the new `IMyDependency` implementation:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

<span data-ttu-id="bba0b-142">`MyDependency2` hängt von der <xref:Microsoft.Extensions.Logging.ILogger%601>-Schnittstelle ab, die im Konstruktor angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-142">`MyDependency2` depends on <xref:Microsoft.Extensions.Logging.ILogger%601>, which it requests in the constructor.</span></span> <span data-ttu-id="bba0b-143">`ILogger<TCategoryName>` ist ein [vom Framework bereitgestellter Dienst](#framework-provided-services).</span><span class="sxs-lookup"><span data-stu-id="bba0b-143">`ILogger<TCategoryName>` is a [framework-provided service](#framework-provided-services).</span></span>

<span data-ttu-id="bba0b-144">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-144">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="bba0b-145">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="bba0b-145">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="bba0b-146">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="bba0b-146">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="bba0b-147">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-147">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="bba0b-148">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch nicht mehr jeder [(generische) konstruierte Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) registriert werden muss:</span><span class="sxs-lookup"><span data-stu-id="bba0b-148">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types).</span></span>

<span data-ttu-id="bba0b-149">Im Dependency-Injection-Kontext ist ein Dienst:</span><span class="sxs-lookup"><span data-stu-id="bba0b-149">In dependency injection terminology, a service:</span></span>

* <span data-ttu-id="bba0b-150">in der Regel ein Objekt, das einen Dienst für andere Objekte bereitstellt, z. B. den `IMyDependency`-Dienst.</span><span class="sxs-lookup"><span data-stu-id="bba0b-150">Is typically an object that provides a service to other objects, such as the `IMyDependency` service.</span></span>
* <span data-ttu-id="bba0b-151">kein Webdienst, obwohl ein Dienst einen Webdienst verwenden kann</span><span class="sxs-lookup"><span data-stu-id="bba0b-151">Is not related to a web service, although the service may use a web service.</span></span>

<span data-ttu-id="bba0b-152">Das Framework bietet eine stabiles [Protokollierungssystem](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="bba0b-152">The framework provides a robust [logging](xref:fundamentals/logging/index) system.</span></span> <span data-ttu-id="bba0b-153">Die in den vorherigen Beispielen veranschaulichten `IMyDependency`-Implementierungen wurden geschrieben, um die grundlegende Dependency Injection zu demonstrieren, nicht zum Implementieren der Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="bba0b-153">The `IMyDependency` implementations shown in the preceding examples were written to demonstrate basic DI, not to implement logging.</span></span> <span data-ttu-id="bba0b-154">Die meisten Apps müssen keine Protokollierungen schreiben.</span><span class="sxs-lookup"><span data-stu-id="bba0b-154">Most apps shouldn't need to write loggers.</span></span> <span data-ttu-id="bba0b-155">Der folgende Code veranschaulicht die Verwendung der Standardprotokollierung, bei der keine Dienste in `ConfigureServices` registriert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="bba0b-155">The following code demonstrates using the default logging, which doesn't require any services to be registered in `ConfigureServices`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="bba0b-156">Bei Verwendung des vorangehenden Codes muss `ConfigureServices` nicht aktualisiert werden, weil die [Protokollierung](xref:fundamentals/logging/index) vom Framework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-156">Using the preceding code, there is no need to update `ConfigureServices`, because [logging](xref:fundamentals/logging/index) is provided by the framework.</span></span>

## <a name="services-injected-into-startup"></a><span data-ttu-id="bba0b-157">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="bba0b-157">Services injected into Startup</span></span>

<span data-ttu-id="bba0b-158">Dienste können in den `Startup`-Konstruktor und die `Startup.Configure`-Methode eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-158">Services can be injected into the `Startup` constructor and the `Startup.Configure` method.</span></span>

<span data-ttu-id="bba0b-159">Nur die folgenden Dienste können in den `Startup`-Konstruktor eingefügt werden, wenn der generische Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="bba0b-159">Only the following services can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="bba0b-160">Jeder Dienst, der mit dem DI-Container registriert wurde, kann in die `Startup.Configure`-Methode eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-160">Any service registered with the DI container can be injected into the `Startup.Configure` method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

<span data-ttu-id="bba0b-161">Weitere Informationen finden Sie unter <xref:fundamentals/startup> und unter [Zugriffskonfiguration beim Start](xref:fundamentals/configuration/index#access-configuration-in-startup).</span><span class="sxs-lookup"><span data-stu-id="bba0b-161">For more information, see <xref:fundamentals/startup> and [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup).</span></span>

## <a name="register-groups-of-services-with-extension-methods"></a><span data-ttu-id="bba0b-162">Registrieren von Dienstgruppen mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="bba0b-162">Register groups of services with extension methods</span></span>

<span data-ttu-id="bba0b-163">Das ASP.NET Core-Framework verwendet eine Konvention zum Registrieren einer Gruppe verwandter Dienste.</span><span class="sxs-lookup"><span data-stu-id="bba0b-163">The ASP.NET Core framework uses a convention for registering a group of related services.</span></span> <span data-ttu-id="bba0b-164">Die Konvention besteht darin, eine einzelne `Add{GROUP_NAME}`-Erweiterungsmethode zum Registrieren aller Dienste zu verwenden, die von einem Frameworkfeature benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-164">The convention is to use a single `Add{GROUP_NAME}` extension method to register all of the services required by a framework feature.</span></span> <span data-ttu-id="bba0b-165">Beispielsweise registriert die Erweiterungsmethode <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> die Dienste, die für MVC-Controller erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="bba0b-165">For example, the <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> extension method registers the services required for MVC controllers.</span></span>

<span data-ttu-id="bba0b-166">Der folgende Code wird von der Razor Pages-Vorlage auf Grundlage einzelner Benutzerkonten generiert. Er veranschaulicht, wie mit den Erweiterungsmethoden <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> zusätzliche Dienste zum Container hinzugefügt werden können:</span><span class="sxs-lookup"><span data-stu-id="bba0b-166">The following code is generated by the Razor Pages template using individual user accounts and shows how to add additional services to the container using the extension methods <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A>:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a><span data-ttu-id="bba0b-167">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="bba0b-167">Service lifetimes</span></span>

<span data-ttu-id="bba0b-168">Dienste können mit einer den folgenden Lebensdaueroptionen registriert werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-168">Services can be registered with one of the following lifetimes:</span></span>

* <span data-ttu-id="bba0b-169">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-169">Transient</span></span>
* <span data-ttu-id="bba0b-170">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="bba0b-170">Scoped</span></span>
* <span data-ttu-id="bba0b-171">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-171">Singleton</span></span>

<span data-ttu-id="bba0b-172">In den folgenden Abschnitten werden die einzelnen genannten Lebensdaueroptionen beschrieben.</span><span class="sxs-lookup"><span data-stu-id="bba0b-172">The following sections describe each of the preceding lifetimes.</span></span> <span data-ttu-id="bba0b-173">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="bba0b-173">Choose an appropriate lifetime for each registered service.</span></span> 

### <a name="transient"></a><span data-ttu-id="bba0b-174">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-174">Transient</span></span>

<span data-ttu-id="bba0b-175">Kurzlebige Dienste werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-175">Transient lifetime services are created each time they're requested from the service container.</span></span> <span data-ttu-id="bba0b-176">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-176">This lifetime works best for lightweight, stateless services.</span></span> <span data-ttu-id="bba0b-177">Vorübergehende Dienste werden mit <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> registriert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-177">Register transient services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A>.</span></span>

<span data-ttu-id="bba0b-178">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-178">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="bba0b-179">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="bba0b-179">Scoped</span></span>

<span data-ttu-id="bba0b-180">Dienste mit bereichsbezogener Lebensdauer werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-180">Scoped lifetime services are created once per client request (connection).</span></span> <span data-ttu-id="bba0b-181">Bereichsbezogene Dienste werden mit <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> registriert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-181">Register scoped services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>.</span></span>

<span data-ttu-id="bba0b-182">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-182">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

<span data-ttu-id="bba0b-183">Wenn Sie Entity Framework Core verwenden, registriert die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterungsmethode standardmäßig `DbContext`-Typen mit einer begrenzten Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="bba0b-183">When using Entity Framework Core, the <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> extension method registers `DbContext` types with a scoped lifetime by default.</span></span>

<span data-ttu-id="bba0b-184">Lösen Sie einen bereichsbezogenen Dienst ***nicht*** über ein Singleton auf.</span><span class="sxs-lookup"><span data-stu-id="bba0b-184">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="bba0b-185">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="bba0b-185">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="bba0b-186">Folgendes ist zulässig:</span><span class="sxs-lookup"><span data-stu-id="bba0b-186">It's fine to:</span></span>

* <span data-ttu-id="bba0b-187">das Auflösen eines Singletondiensts aus einem bereichsbezogenen oder temporären Diensts.</span><span class="sxs-lookup"><span data-stu-id="bba0b-187">Resolve a singleton service from a scoped or transient service.</span></span>
* <span data-ttu-id="bba0b-188">das Auflösen eines bereichsbezogenen Diensts aus einem anderen bereichsbezogenen oder temporären Dienst</span><span class="sxs-lookup"><span data-stu-id="bba0b-188">Resolve a scoped service from another scoped or transient service.</span></span>

<span data-ttu-id="bba0b-189">Standardmäßig wird in der Entwicklungsumgebung eine Ausnahme ausgelöst, wenn ein Dienst von einem anderen Dienst mit längerer Lebensdauer aufgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-189">By default, in the development environment, resolving a service from another service with a longer lifetime throws an exception.</span></span> <span data-ttu-id="bba0b-190">Weitere Informationen finden Sie unter [Bereichsvalidierung](#sv).</span><span class="sxs-lookup"><span data-stu-id="bba0b-190">For more information, see [Scope validation](#sv).</span></span>

<span data-ttu-id="bba0b-191">Zum Verwenden bereichsbezogener Dienste in Middleware, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="bba0b-191">To use scoped services in middleware, use one of the following approaches:</span></span>

* <span data-ttu-id="bba0b-192">Fügen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode der Middleware ein.</span><span class="sxs-lookup"><span data-stu-id="bba0b-192">Inject the service into the middleware's `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="bba0b-193">Bei Verwendung der [Constructor Injection](xref:mvc/controllers/dependency-injection#constructor-injection) wird eine Runtimeausnahme ausgelöst, weil dem bereichsbezogenen Dienst das Verhalten eines Singletons aufgezwungen wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-193">Using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) throws a runtime exception because it forces the scoped service to behave like a singleton.</span></span> <span data-ttu-id="bba0b-194">Im Beispiel des Abschnitts [Lebensdauer und Registrierungsoptionen](#lifetime-and-registration-options) wird der `InvokeAsync`-Ansatz veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="bba0b-194">The sample in the [Lifetime and registration options](#lifetime-and-registration-options) section demonstrates the `InvokeAsync` approach.</span></span>
* <span data-ttu-id="bba0b-195">Verwenden Sie [factorybezogene Middleware](xref:fundamentals/middleware/extensibility).</span><span class="sxs-lookup"><span data-stu-id="bba0b-195">Use [Factory-based middleware](xref:fundamentals/middleware/extensibility).</span></span> <span data-ttu-id="bba0b-196">Middleware, die mit diesem Ansatz registriert wurde, wird pro Clientanforderung (Verbindung) aktiviert, wodurch bereichsbezogene Dienste in die `InvokeAsync`-Methode der Middleware eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="bba0b-196">Middleware registered using this approach is activated per client request (connection), which allows scoped services to be injected into the middleware's `InvokeAsync` method.</span></span>

<span data-ttu-id="bba0b-197">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="bba0b-197">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="bba0b-198">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-198">Singleton</span></span>

<span data-ttu-id="bba0b-199">Dienste mit Singletonlebensdauer werden erstellt:</span><span class="sxs-lookup"><span data-stu-id="bba0b-199">Singleton lifetime services are created either:</span></span>

* <span data-ttu-id="bba0b-200">wenn sie zum ersten Mal angefordert werden</span><span class="sxs-lookup"><span data-stu-id="bba0b-200">The first time they're requested.</span></span>
* <span data-ttu-id="bba0b-201">vom Entwickler, wenn eine Implementierungsinstanz direkt im Container bereitgestellt wird</span><span class="sxs-lookup"><span data-stu-id="bba0b-201">By the developer, when providing an implementation instance directly to the container.</span></span> <span data-ttu-id="bba0b-202">(selten benötigter Ansatz)</span><span class="sxs-lookup"><span data-stu-id="bba0b-202">This approach is rarely needed.</span></span>

<span data-ttu-id="bba0b-203">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="bba0b-203">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="bba0b-204">Wenn für die App Singletonverhalten erforderlich ist, sollte der Dienstcontainer die Dienstlebensdauer verwalten.</span><span class="sxs-lookup"><span data-stu-id="bba0b-204">If the app requires singleton behavior, allow the service container to manage the service's lifetime.</span></span> <span data-ttu-id="bba0b-205">Wenn Sie das Singletonentwurfsmuster implementieren, sollten Sie keinen Code zum Löschen des Singleton angeben.</span><span class="sxs-lookup"><span data-stu-id="bba0b-205">Don't implement the singleton design pattern and provide code to dispose of the singleton.</span></span> <span data-ttu-id="bba0b-206">Dienste sollten nicht durch den Code gelöscht werden können, der den Dienst aus einem Container aufgelöst hat.</span><span class="sxs-lookup"><span data-stu-id="bba0b-206">Services should never be disposed by code that resolved the service from the container.</span></span> <span data-ttu-id="bba0b-207">Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton automatisch vom Container verworfen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-207">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="bba0b-208">Registrieren Sie Singletondienste mit <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span><span class="sxs-lookup"><span data-stu-id="bba0b-208">Register singleton services with <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>.</span></span> <span data-ttu-id="bba0b-209">Singletondienste müssen threadsicher sein und werden häufig in zustandslosen Diensten verwendet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-209">Singleton services must be thread safe and are often used in stateless services.</span></span>

<span data-ttu-id="bba0b-210">In Apps, die Anforderungen verarbeiten, werden Singletondienste gelöscht, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-210">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed on application shutdown.</span></span> <span data-ttu-id="bba0b-211">Da der Arbeitsspeicher erst freigegeben wird, wenn die App heruntergefahren wird, müssen Sie den Arbeitsspeicherverbrauch eines Singletons berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-211">Because memory is not released until the app is shut down, consider memory use with a singleton service.</span></span>

> [!WARNING]
> <span data-ttu-id="bba0b-212">Lösen Sie einen bereichsbezogenen Dienst ***nicht*** über ein Singleton auf.</span><span class="sxs-lookup"><span data-stu-id="bba0b-212">Do ***not*** resolve a scoped service from a singleton.</span></span> <span data-ttu-id="bba0b-213">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="bba0b-213">It may cause the service to have incorrect state when processing subsequent requests.</span></span> <span data-ttu-id="bba0b-214">Sie können einen Singletondienst aus einem bereichsbezogenen oder temporären Dienst auflösen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-214">It's fine to resolve a singleton service from a scoped or transient service.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="bba0b-215">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="bba0b-215">Service registration methods</span></span>

<span data-ttu-id="bba0b-216">Das Framework stellt Erweiterungsmethoden für die Dienstregistrierung bereit, die in bestimmten Szenarios hilfreich sind:</span><span class="sxs-lookup"><span data-stu-id="bba0b-216">The framework provides service registration extension methods that are useful in specific scenarios:</span></span>

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| <span data-ttu-id="bba0b-217">Methode</span><span class="sxs-lookup"><span data-stu-id="bba0b-217">Method</span></span>                                                                                                                                                                              | <span data-ttu-id="bba0b-218">Automatische</span><span class="sxs-lookup"><span data-stu-id="bba0b-218">Automatic</span></span><br><span data-ttu-id="bba0b-219">Objekt</span><span class="sxs-lookup"><span data-stu-id="bba0b-219">object</span></span><br><span data-ttu-id="bba0b-220">bereinigung</span><span class="sxs-lookup"><span data-stu-id="bba0b-220">disposal</span></span> | <span data-ttu-id="bba0b-221">Mehrere</span><span class="sxs-lookup"><span data-stu-id="bba0b-221">Multiple</span></span><br><span data-ttu-id="bba0b-222">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="bba0b-222">implementations</span></span> | <span data-ttu-id="bba0b-223">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="bba0b-223">Pass args</span></span> |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="bba0b-224">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bba0b-224">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | <span data-ttu-id="bba0b-225">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-225">Yes</span></span>                             | <span data-ttu-id="bba0b-226">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-226">Yes</span></span>                         | <span data-ttu-id="bba0b-227">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-227">No</span></span>        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="bba0b-228">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="bba0b-228">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | <span data-ttu-id="bba0b-229">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-229">Yes</span></span>                             | <span data-ttu-id="bba0b-230">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-230">Yes</span></span>                         | <span data-ttu-id="bba0b-231">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-231">Yes</span></span>       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="bba0b-232">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bba0b-232">Example:</span></span><br>`services.AddSingleton<MyDep>();`                                                                                                | <span data-ttu-id="bba0b-233">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-233">Yes</span></span>                             | <span data-ttu-id="bba0b-234">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-234">No</span></span>                          | <span data-ttu-id="bba0b-235">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-235">No</span></span>        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="bba0b-236">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="bba0b-236">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | <span data-ttu-id="bba0b-237">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-237">No</span></span>                              | <span data-ttu-id="bba0b-238">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-238">Yes</span></span>                         | <span data-ttu-id="bba0b-239">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-239">Yes</span></span>       |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="bba0b-240">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="bba0b-240">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | <span data-ttu-id="bba0b-241">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-241">No</span></span>                              | <span data-ttu-id="bba0b-242">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-242">No</span></span>                          | <span data-ttu-id="bba0b-243">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-243">Yes</span></span>       |

<span data-ttu-id="bba0b-244">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="bba0b-244">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="bba0b-245">Es ist üblich, mehrere Implementierungen zu verwenden, [wenn Typen zu Testzecken simuliert werden](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="bba0b-245">It's common to use multiple implementations when [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="bba0b-246">Das Framework stellt außerdem `TryAdd{LIFETIME}`-Erweiterungsmethoden bereit, die den Dienst nur registrieren, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-246">The framework also provides `TryAdd{LIFETIME}` extension methods, which register the service only if there isn't already an implementation registered.</span></span>

<span data-ttu-id="bba0b-247">Im folgenden Beispiel registriert der `AddSingleton`-Aufruf `MyDependency` als Implementierung für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-247">In the following example, the call to `AddSingleton` registers `MyDependency` as an implementation for `IMyDependency`.</span></span> <span data-ttu-id="bba0b-248">Der Aufruf von `TryAddSingleton` hat keine Auswirkung, weil `IMyDependency` bereits eine registrierte Implementierung aufweist:</span><span class="sxs-lookup"><span data-stu-id="bba0b-248">The call to `TryAddSingleton` has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="bba0b-249">Weitere Informationen finden Sie unter</span><span class="sxs-lookup"><span data-stu-id="bba0b-249">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

<span data-ttu-id="bba0b-250">Die [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A)-Methoden registrieren den Dienst nur, wenn noch keine Implementierung *desselben Typs* vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-250">The [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A) methods register the service only if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="bba0b-251">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="bba0b-251">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="bba0b-252">Beim Registrieren von Diensten sollte der Entwickler nur dann eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="bba0b-252">When registering services, the developer should add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="bba0b-253">Im Allgemeinen verwenden Bibliotheksautoren `TryAddEnumerable`, um zu vermeiden, dass mehrere Kopien einer Implementierung im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-253">Generally, library authors use `TryAddEnumerable` to avoid registering multiple copies of an implementation in the container.</span></span>

<span data-ttu-id="bba0b-254">Im folgenden Beispiel registriert der erste `TryAddEnumerable`-Aufruf `MyDependency` als Implementierung für `IMyDependency1`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-254">In the following example, the first call to `TryAddEnumerable` registers `MyDependency` as an implementation for `IMyDependency1`.</span></span> <span data-ttu-id="bba0b-255">Der zweite Aufruf registriert `MyDependency` für `IMyDependency2`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-255">The second call registers `MyDependency` for `IMyDependency2`.</span></span> <span data-ttu-id="bba0b-256">Der dritte Aufruf hat keine Auswirkungen, da `IMyDependency1` bereits eine registrierte Implementierung von `MyDependency` aufweist:</span><span class="sxs-lookup"><span data-stu-id="bba0b-256">The third call has no effect because `IMyDependency1` already has a registered implementation of `MyDependency`:</span></span>

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

<span data-ttu-id="bba0b-257">Bei der Dienstregistrierung ist die Reihenfolge unerheblich, wenn mehrere Implementierungen desselben Typs registriert werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-257">Service registration is generally order independent except when registering multiple implementations of the same type.</span></span>

<span data-ttu-id="bba0b-258">`IServiceCollection` ist eine Sammlung von <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>-Objekten.</span><span class="sxs-lookup"><span data-stu-id="bba0b-258">`IServiceCollection` is a collection of <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor> objects.</span></span> <span data-ttu-id="bba0b-259">Im folgenden Beispiel wird gezeigt, wie ein Dienst durch Erstellen und Hinzufügen von `ServiceDescriptor` registriert wird:</span><span class="sxs-lookup"><span data-stu-id="bba0b-259">The following example shows how to register a service by creating and adding a `ServiceDescriptor`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

<span data-ttu-id="bba0b-260">Die integrierten `Add{LIFETIME}`-Methoden verwenden denselben Ansatz.</span><span class="sxs-lookup"><span data-stu-id="bba0b-260">The built-in `Add{LIFETIME}` methods use the same approach.</span></span> <span data-ttu-id="bba0b-261">Ein Beispiel finden Sie im [Quellcode für AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span><span class="sxs-lookup"><span data-stu-id="bba0b-261">For example, see the [AddScoped source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).</span></span>

### <a name="constructor-injection-behavior"></a><span data-ttu-id="bba0b-262">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="bba0b-262">Constructor injection behavior</span></span>

<span data-ttu-id="bba0b-263">Dienste können mithilfe der folgenden Ansätze aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-263">Services can be resolved by using:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="bba0b-264"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span><span class="sxs-lookup"><span data-stu-id="bba0b-264"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:</span></span>
  * <span data-ttu-id="bba0b-265">erstellt Objekte, die nicht im Container registriert sind.</span><span class="sxs-lookup"><span data-stu-id="bba0b-265">Creates objects that aren't registered in the container.</span></span>
  * <span data-ttu-id="bba0b-266">wird mit Frameworkfeatures wie [Taghilfsprogrammen](xref:mvc/views/tag-helpers/intro), MVC-Controllern und [Modellbindungen](xref:mvc/models/model-binding) verwendet</span><span class="sxs-lookup"><span data-stu-id="bba0b-266">Used with framework features, such as [Tag Helpers](xref:mvc/views/tag-helpers/intro), MVC controllers, and [model binders](xref:mvc/models/model-binding).</span></span>

<span data-ttu-id="bba0b-267">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-267">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="bba0b-268">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="bba0b-268">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="bba0b-269">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-269">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="bba0b-270">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="bba0b-270">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="bba0b-271">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="bba0b-271">Entity Framework contexts</span></span>

<span data-ttu-id="bba0b-272">Entity Framework-Kontexte werden einem Dienstcontainer standardmäßig mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-272">By default, Entity Framework contexts are added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="bba0b-273">Legen Sie die Lebensdauer mithilfe einer <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Überladung fest, um eine andere Lebensdauer zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-273">To use a different lifetime, specify the lifetime by using an <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> overload.</span></span> <span data-ttu-id="bba0b-274">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer Lebensdauer verwenden, die kürzer als die Lebensdauer des Diensts ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-274">Services of a given lifetime shouldn't use a database context with a lifetime that's shorter than the service's lifetime.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="bba0b-275">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="bba0b-275">Lifetime and registration options</span></span>

<span data-ttu-id="bba0b-276">In den folgenden Schnittstellen, die einen Task als Vorgang mit einem Bezeichner (`OperationId`) darstellen, wird der Unterschied zwischen Dienstlebensdauern und ihren Registrierungsoptionen veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="bba0b-276">To demonstrate the difference between service lifetimes and their registration options, consider the following interfaces that represent a task as an operation with an identifier, `OperationId`.</span></span> <span data-ttu-id="bba0b-277">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="bba0b-277">Depending on how the lifetime of an operation's service is configured for the following interfaces, the container provides either the same or different instances of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="bba0b-278">Die folgende `Operation`-Klasse implementiert alle vorangehenden Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-278">The following `Operation` class implements all of the preceding interfaces.</span></span> <span data-ttu-id="bba0b-279">Der `Operation`-Konstruktor generiert einen eindeutigen Bezeichner (GUID) und speichert die letzten 4 Zeichen in der `OperationId`-Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="bba0b-279">The `Operation` constructor generates a GUID and stores the last 4 characters in the `OperationId` property:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

<span data-ttu-id="bba0b-280">Die `Startup.ConfigureServices`-Methode erstellt mehrere Registrierungen der `Operation`-Klasse entsprechend der benannten Lebensdauern:</span><span class="sxs-lookup"><span data-stu-id="bba0b-280">The `Startup.ConfigureServices` method creates multiple registrations of the `Operation` class according to the named lifetimes:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

<span data-ttu-id="bba0b-281">Die Beispiel-App veranschaulicht die Objektlebensdauer sowohl in als auch zwischen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-281">The sample app demonstrates object lifetimes both within and between requests.</span></span> <span data-ttu-id="bba0b-282">`IndexModel` und die Middleware fordern jede Art von `IOperation`-Typ an und protokollieren `OperationId` für jeden Typ:</span><span class="sxs-lookup"><span data-stu-id="bba0b-282">The `IndexModel` and the middleware request each kind of `IOperation` type and log the `OperationId` for each:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="bba0b-283">Ähnlich wie `IndexModel` löst die Middleware dieselben Dienste auf:</span><span class="sxs-lookup"><span data-stu-id="bba0b-283">Similar to the `IndexModel`, the middleware resolves the same services:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

<span data-ttu-id="bba0b-284">Bereichsbezogene Dienste müssen in der `InvokeAsync`-Methode aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-284">Scoped services must be resolved in the `InvokeAsync` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

<span data-ttu-id="bba0b-285">Die Protokollierungsausgabe zeigt Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="bba0b-285">The logger output shows:</span></span>

* <span data-ttu-id="bba0b-286">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="bba0b-286">*Transient* objects are always different.</span></span> <span data-ttu-id="bba0b-287">Der vorübergehende `OperationId`-Wert unterscheidet sich in `IndexModel` und der Middleware.</span><span class="sxs-lookup"><span data-stu-id="bba0b-287">The transient `OperationId` value is different in the `IndexModel` and in the middleware.</span></span>
* <span data-ttu-id="bba0b-288">*Bereichsbezogene* Objekte sind für jede Anforderung identisch, aber unterscheiden sich für jede Anforderung.</span><span class="sxs-lookup"><span data-stu-id="bba0b-288">*Scoped* objects are the same for each request but different across each request.</span></span>
* <span data-ttu-id="bba0b-289">*Singletonobjekte* sind für jede Anforderung identisch.</span><span class="sxs-lookup"><span data-stu-id="bba0b-289">*Singleton* objects are the same for every request.</span></span>

<span data-ttu-id="bba0b-290">Sie können "Logging:LogLevel:Microsoft:Error" in der Datei *appsettings.Development.json* festlegen, um die Protokollierungsausgabe zu reduzieren:</span><span class="sxs-lookup"><span data-stu-id="bba0b-290">To reduce the logging output, set "Logging:LogLevel:Microsoft:Error" in the *appsettings.Development.json* file:</span></span>

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a><span data-ttu-id="bba0b-291">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="bba0b-291">Call services from main</span></span>

<span data-ttu-id="bba0b-292">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-292">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="bba0b-293">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-293">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span>

<span data-ttu-id="bba0b-294">Im folgenden Beispiel wird der Zugriff auf den bereichsbezogenen `IMyDependency`-Dienst und der Aufruf dessen `WriteMessage`-Methode in `Program.Main` veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="bba0b-294">The following example shows how to access the scoped `IMyDependency` service and call its `WriteMessage` method in `Program.Main`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a><span data-ttu-id="bba0b-295">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="bba0b-295">Scope validation</span></span>

<span data-ttu-id="bba0b-296">Wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird und [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) aufruft, um den Host zu erstellen, stellt der Standarddienstanbieter sicher, dass:</span><span class="sxs-lookup"><span data-stu-id="bba0b-296">When the app runs in the [Development environment](xref:fundamentals/environments) and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="bba0b-297">bereichsbezogene Dienste nicht vom Stammdienstanbieter aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-297">Scoped services aren't resolved from the root service provider.</span></span>
* <span data-ttu-id="bba0b-298">bereichsbezogene Dienste nicht in Singletons eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-298">Scoped services aren't injected into singletons.</span></span>

<span data-ttu-id="bba0b-299">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-299">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> is called.</span></span> <span data-ttu-id="bba0b-300">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App, wenn der Anbieter beim Start der App erstellt und beim Herunterfahren gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-300">The root service provider's lifetime corresponds to the app's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="bba0b-301">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="bba0b-301">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="bba0b-302">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer quasi auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-302">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when the app shuts down.</span></span> <span data-ttu-id="bba0b-303">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-303">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="bba0b-304">Weitere Informationen finden Sie unter [Bereichsvalidierung](xref:fundamentals/host/web-host#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="bba0b-304">For more information, see [Scope validation](xref:fundamentals/host/web-host#scope-validation).</span></span>

## <a name="request-services"></a><span data-ttu-id="bba0b-305">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="bba0b-305">Request Services</span></span>

<span data-ttu-id="bba0b-306">Die in einer ASP.NET Core-Anforderung verfügbaren Dienste werden über die Sammlung [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-306">The services available within an ASP.NET Core request are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span> <span data-ttu-id="bba0b-307">Wenn Dienste innerhalb einer Anforderung angefordert werden, werden die Dienste und ihre Abhängigkeiten aus der `RequestServices`-Sammlung aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="bba0b-307">When services are requested from inside of a request, the services and their dependencies are resolved from the `RequestServices` collection.</span></span>

<span data-ttu-id="bba0b-308">Das Framework erstellt einen Bereich pro Anforderung, und `RequestServices` stellt den bereichsbezogenen Dienstanbieter zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="bba0b-308">The framework creates a scope per request and `RequestServices` exposes the scoped service provider.</span></span> <span data-ttu-id="bba0b-309">Alle bereichsbezogenen Dienste sind gültig, solange die Anforderung aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-309">All scoped services are valid for as long as the request is active.</span></span>

> [!NOTE]
> <span data-ttu-id="bba0b-310">Sie sollten das Anfordern von Abhängigkeiten als Konstruktorparameter zum Auflösen von Diensten aus der `RequestServices`-Sammlung bevorzugen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-310">Prefer requesting dependencies as constructor parameters to resolving services from the `RequestServices` collection.</span></span> <span data-ttu-id="bba0b-311">Dies resultiert in Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="bba0b-311">This results in classes that are easier to test.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="bba0b-312">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="bba0b-312">Design services for dependency injection</span></span>

<span data-ttu-id="bba0b-313">Beachten Sie Folgendes beim Entwerfen von Diensten für Dependency Injection:</span><span class="sxs-lookup"><span data-stu-id="bba0b-313">When designing services for dependency injection:</span></span>

* <span data-ttu-id="bba0b-314">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="bba0b-314">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="bba0b-315">Vermeiden Sie das Erstellen eines globalen Zustands, indem Sie Apps stattdessen zur Verwendung von Singletondiensten entwerfen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-315">Avoid creating global state by designing apps to use singleton services instead.</span></span>
* <span data-ttu-id="bba0b-316">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="bba0b-316">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="bba0b-317">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="bba0b-317">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="bba0b-318">Erstellen Sie kleine, gut gestaltete und einfach zu testende Dienste.</span><span class="sxs-lookup"><span data-stu-id="bba0b-318">Make services small, well-factored, and easily tested.</span></span>

<span data-ttu-id="bba0b-319">Wenn eine Klasse viele eingefügte Abhängigkeiten aufweist, ist dies möglicherweise ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Prinzip der einzelnen Verantwortung (SRP, Single Responsibility Principle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) verstößt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-319">If a class has a lot of injected dependencies, it might be a sign that the class has too many responsibilities and violates the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="bba0b-320">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Verantwortung in neue Klassen verschieben.</span><span class="sxs-lookup"><span data-stu-id="bba0b-320">Attempt to refactor the class by moving some of its responsibilities into new classes.</span></span> <span data-ttu-id="bba0b-321">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-321">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="bba0b-322">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="bba0b-322">Disposal of services</span></span>

<span data-ttu-id="bba0b-323">Der Container ruft <xref:System.IDisposable.Dispose%2A> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="bba0b-323">The container calls <xref:System.IDisposable.Dispose%2A> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="bba0b-324">Dienste, die aus dem Container aufgelöst werden, sollten nie vom Entwickler gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-324">Services resolved from the container should never be disposed by the developer.</span></span> <span data-ttu-id="bba0b-325">Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton automatisch vom Container verworfen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-325">If a type or factory is registered as a singleton, the container disposes the singleton automatically.</span></span>

<span data-ttu-id="bba0b-326">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="bba0b-326">In the following example, the services are created by the service container and disposed automatically:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="bba0b-327">In der Debuggingkonsole wird nach jeder Aktualisierung der Indexseite die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="bba0b-327">The debug console shows the following output after each refresh of the Index page:</span></span>

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a><span data-ttu-id="bba0b-328">Nicht vom Dienstcontainer erstellte Dienste</span><span class="sxs-lookup"><span data-stu-id="bba0b-328">Services not created by the service container</span></span>

<span data-ttu-id="bba0b-329">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bba0b-329">Consider the following code:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

<span data-ttu-id="bba0b-330">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="bba0b-330">In the preceding code:</span></span>

* <span data-ttu-id="bba0b-331">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-331">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="bba0b-332">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="bba0b-332">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="bba0b-333">Der Entwickler ist für das Löschen der Dienste verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="bba0b-333">The developer is responsible for disposing the services.</span></span>

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="bba0b-334">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="bba0b-334">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="bba0b-335">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="bba0b-335">Transient, limited lifetime</span></span>

<span data-ttu-id="bba0b-336">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="bba0b-336">**Scenario**</span></span>

<span data-ttu-id="bba0b-337">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="bba0b-337">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="bba0b-338">Die Instanz wird im Stammbereich (Stammcontainer) aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="bba0b-338">The instance is resolved in the root scope (root container).</span></span>
* <span data-ttu-id="bba0b-339">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-339">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="bba0b-340">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="bba0b-340">**Solution**</span></span>

<span data-ttu-id="bba0b-341">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-341">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="bba0b-342">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="bba0b-342">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="bba0b-343">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="bba0b-343">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="bba0b-344">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-344">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="bba0b-345">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, um die Instanz außerhalb des Containers zu instanziieren, während Sie den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-345">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside of the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="bba0b-346">Freigegebene Instanz (eingeschränkte Lebensdauer)</span><span class="sxs-lookup"><span data-stu-id="bba0b-346">Shared instance, limited lifetime</span></span>

<span data-ttu-id="bba0b-347">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="bba0b-347">**Scenario**</span></span>

<span data-ttu-id="bba0b-348">Die App erfordert eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste, jedoch sollte die <xref:System.IDisposable>-Instanz eine begrenzte Lebensdauer aufweisen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-348">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> instance should have a limited lifetime.</span></span>

<span data-ttu-id="bba0b-349">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="bba0b-349">**Solution**</span></span>

<span data-ttu-id="bba0b-350">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="bba0b-350">Register the instance with a scoped lifetime.</span></span> <span data-ttu-id="bba0b-351">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>, um eine neue <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>-Schnittstelle zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-351">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="bba0b-352">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-352">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="bba0b-353">Löschen Sie den Bereich, wenn er nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-353">Dispose the scope when it's no longer needed.</span></span>

#### <a name="general-idisposable-guidelines"></a><span data-ttu-id="bba0b-354">Allgemeine Richtlinien für IDisposable</span><span class="sxs-lookup"><span data-stu-id="bba0b-354">General IDisposable guidelines</span></span>

* <span data-ttu-id="bba0b-355">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einer vorübergehenden Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="bba0b-355">Don't register <xref:System.IDisposable> instances with a transient lifetime.</span></span> <span data-ttu-id="bba0b-356">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="bba0b-356">Use the factory pattern instead.</span></span>
* <span data-ttu-id="bba0b-357">Lösen Sie keine <xref:System.IDisposable>-Instanzen mit vorübergehender oder bereichsbezogener Lebensdauer im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="bba0b-357">Don't resolve <xref:System.IDisposable> instances with a transient or scoped lifetime in the root scope.</span></span> <span data-ttu-id="bba0b-358">Die einzige Ausnahme hierfür besteht in dem Fall, dass die App <xref:System.IServiceProvider> erstellt oder neu erstellt und löscht. Dieses Muster ist jedoch nicht ideal.</span><span class="sxs-lookup"><span data-stu-id="bba0b-358">The only exception to this is if the app creates/recreates and disposes <xref:System.IServiceProvider>, but this isn't an ideal pattern.</span></span>
* <span data-ttu-id="bba0b-359">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-359">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="bba0b-360">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-360">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="bba0b-361">Verwenden Sie Bereiche, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="bba0b-361">Use scopes to control the lifetimes of services.</span></span> <span data-ttu-id="bba0b-362">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-362">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="bba0b-363">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="bba0b-363">Default service container replacement</span></span>

<span data-ttu-id="bba0b-364">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-364">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="bba0b-365">Die Verwendung der integrierten Container wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das nicht unterstützt wird, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bba0b-365">We recommend using the built-in container unless you need a specific feature that it doesn't support, such as:</span></span>

* <span data-ttu-id="bba0b-366">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="bba0b-366">Property injection</span></span>
* <span data-ttu-id="bba0b-367">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="bba0b-367">Injection based on name</span></span>
* <span data-ttu-id="bba0b-368">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="bba0b-368">Child containers</span></span>
* <span data-ttu-id="bba0b-369">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="bba0b-369">Custom lifetime management</span></span>
* <span data-ttu-id="bba0b-370">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="bba0b-370">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="bba0b-371">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="bba0b-371">Convention-based registration</span></span>

<span data-ttu-id="bba0b-372">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-372">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="bba0b-373">Autofac</span><span class="sxs-lookup"><span data-stu-id="bba0b-373">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="bba0b-374">DryIoc</span><span class="sxs-lookup"><span data-stu-id="bba0b-374">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="bba0b-375">Grace</span><span class="sxs-lookup"><span data-stu-id="bba0b-375">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="bba0b-376">LightInject</span><span class="sxs-lookup"><span data-stu-id="bba0b-376">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="bba0b-377">Lamar</span><span class="sxs-lookup"><span data-stu-id="bba0b-377">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="bba0b-378">Stashbox</span><span class="sxs-lookup"><span data-stu-id="bba0b-378">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="bba0b-379">Unity</span><span class="sxs-lookup"><span data-stu-id="bba0b-379">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a><span data-ttu-id="bba0b-380">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="bba0b-380">Thread safety</span></span>

<span data-ttu-id="bba0b-381">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="bba0b-381">Create thread-safe singleton services.</span></span> <span data-ttu-id="bba0b-382">Wenn ein Singletondienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="bba0b-382">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending on how it's used by the singleton.</span></span>

<span data-ttu-id="bba0b-383">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="bba0b-383">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), doesn't need to be thread-safe.</span></span> <span data-ttu-id="bba0b-384">Wie bei Konstruktoren vom Typ `static` erfolgt der Aufruf garantiert nur einmal über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="bba0b-384">Like a type (`static`) constructor, it's guaranteed to be called only once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="bba0b-385">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="bba0b-385">Recommendations</span></span>

* <span data-ttu-id="bba0b-386">Die auf `async/await` und `Task` basierende Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-386">`async/await` and `Task` based service resolution isn't supported.</span></span> <span data-ttu-id="bba0b-387">Da C# asynchrone Konstruktoren nicht unterstützt, verwenden Sie asynchrone Methoden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="bba0b-387">Because C# doesn't support asynchronous constructors, use asynchronous methods after synchronously resolving the service.</span></span>
* <span data-ttu-id="bba0b-388">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="bba0b-388">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="bba0b-389">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-389">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="bba0b-390">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-390">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="bba0b-391">Gleichermaßen sollten Sie „Daten enthaltende“ Objekte vermeiden, die nur dafür vorhanden sind, den Zugriff auf ein anderes Objekt zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-391">Similarly, avoid "data holder" objects that only exist to allow access to another object.</span></span> <span data-ttu-id="bba0b-392">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-392">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="bba0b-393">Vermeiden Sie statischen Zugriff auf Dienste.</span><span class="sxs-lookup"><span data-stu-id="bba0b-393">Avoid static access to services.</span></span> <span data-ttu-id="bba0b-394">Vermeiden Sie beispielsweise das Erfassen von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) als statisches Feld oder als Eigenschaft zur Verwendung an einer anderen Stelle.</span><span class="sxs-lookup"><span data-stu-id="bba0b-394">For example, avoid capturing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) as a static field or property for use elsewhere.</span></span>
* <span data-ttu-id="bba0b-395">DI-Factorys sollten schnell und synchron sein.</span><span class="sxs-lookup"><span data-stu-id="bba0b-395">Keep DI factories fast and synchronous.</span></span>
* <span data-ttu-id="bba0b-396">Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*.</span><span class="sxs-lookup"><span data-stu-id="bba0b-396">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="bba0b-397">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService%2A> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können:</span><span class="sxs-lookup"><span data-stu-id="bba0b-397">For example, don't invoke <xref:System.IServiceProvider.GetService%2A> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="bba0b-398">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="bba0b-398">**Incorrect:**</span></span>

    ![Falscher Code](dependency-injection/_static/bad.png)

  <span data-ttu-id="bba0b-400">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="bba0b-400">**Correct**:</span></span>

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
* <span data-ttu-id="bba0b-401">Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst.</span><span class="sxs-lookup"><span data-stu-id="bba0b-401">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="bba0b-402">Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="bba0b-402">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
* <span data-ttu-id="bba0b-403">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="bba0b-403">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<a name="ASP0000"></a>
* <span data-ttu-id="bba0b-404">Vermeiden Sie Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-404">Avoid calls to <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`.</span></span> <span data-ttu-id="bba0b-405">`BuildServiceProvider` wird in der Regel aufgerufen, wenn der Entwickler einen Dienst in `ConfigureServices` auflösen möchte.</span><span class="sxs-lookup"><span data-stu-id="bba0b-405">Calling `BuildServiceProvider` typically happens when the developer wants to resolve a service in `ConfigureServices`.</span></span> <span data-ttu-id="bba0b-406">Berücksichtigen Sie beispielsweise den Fall, wenn `LoginPath` aus der Konfiguration geladen wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-406">For example, consider the case where the `LoginPath` is loaded from configuration.</span></span> <span data-ttu-id="bba0b-407">Vermeiden Sie den folgenden Ansatz:</span><span class="sxs-lookup"><span data-stu-id="bba0b-407">Avoid the following approach:</span></span>

  ![Ungültiger Code beim Aufruf von BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  <span data-ttu-id="bba0b-409">Wenn Sie auf die grüne Wellenlinie unter `services.BuildServiceProvider` auf der vorherigen Abbildung klicken würden, würde folgende ASP0000-Warnung angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-409">In the preceding image, selecting the green wavy line under `services.BuildServiceProvider` shows the following ASP0000 warning:</span></span>

  > <span data-ttu-id="bba0b-410">ASP0000: Der Aufruf von BuildServiceProvider aus dem Anwendungscode führt dazu, dass eine zusätzliche Kopie der Singletondienste erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-410">ASP0000 Calling 'BuildServiceProvider' from application code results in an additional copy of singleton services being created.</span></span> <span data-ttu-id="bba0b-411">Verwenden Sie Alternativen wie Dependency-Injection-Dienste als Parameter für Configure.</span><span class="sxs-lookup"><span data-stu-id="bba0b-411">Consider alternatives such as dependency injecting services as parameters to 'Configure'.</span></span>

  <span data-ttu-id="bba0b-412">Durch den Aufruf von `BuildServiceProvider` wird ein zweiter Container erstellt, der fragmentierte Singletons und Verweise auf Objektgraphen für mehrere Container verursachen kann.</span><span class="sxs-lookup"><span data-stu-id="bba0b-412">Calling `BuildServiceProvider` creates a second container, which can create torn singletons and cause references to object graphs across multiple containers.</span></span>

  <span data-ttu-id="bba0b-413">Eine korrekte Methode zum Abrufen von `LoginPath` besteht darin, die integrierte Unterstützung von DI des Optionsmusters zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-413">A correct way to get `LoginPath` is to use the options pattern's built-in support for DI:</span></span>

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* <span data-ttu-id="bba0b-414">Löschbare temporäre Dienste werden vom Container für die Löschung erfasst.</span><span class="sxs-lookup"><span data-stu-id="bba0b-414">Disposable transient services are captured by the container for disposal.</span></span> <span data-ttu-id="bba0b-415">Dadurch kann es zu Arbeitsspeicherverlusten kommen, wenn diese vom obersten Container aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-415">This can turn into a memory leak if resolved from the top level container.</span></span>
* <span data-ttu-id="bba0b-416">Aktivieren Sie die Bereichsüberprüfung, um sicherzustellen, dass die App keine Singletons aufweist, die bereichsbezogene Dienste erfassen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-416">Enable scope validation to make sure the app doesn't have singletons that capture scoped services.</span></span> <span data-ttu-id="bba0b-417">Weitere Informationen finden Sie unter [Bereichsvalidierung](#scope-validation).</span><span class="sxs-lookup"><span data-stu-id="bba0b-417">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="bba0b-418">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="bba0b-418">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="bba0b-419">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-419">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="bba0b-420">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="bba0b-420">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="bba0b-421">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="bba0b-421">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="bba0b-422">Empfohlene Muster für Mehrinstanzenfähigkeit in der Dependency Injection (DI)</span><span class="sxs-lookup"><span data-stu-id="bba0b-422">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="bba0b-423">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) ist ein Anwendungsframework zum Erstellen modularer Anwendung mit mehreren Mandanten in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="bba0b-423">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) is an application framework for building modular, multi-tenant applications on ASP.NET Core.</span></span> <span data-ttu-id="bba0b-424">Weitere Informationen finden Sie in der [Orchard Core-Dokumentation](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="bba0b-424">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="bba0b-425">Beispiele zum Erstellen modularer Apps und Apps mit mehreren Mandanten nur mit dem Orchard Core-Framework und ohne den CMS-spezifischen Features finden Sie in den [Orchard Core-Beispielen](https://github.com/OrchardCMS/OrchardCore.Samples).</span><span class="sxs-lookup"><span data-stu-id="bba0b-425">See the [Orchard Core samples](https://github.com/OrchardCMS/OrchardCore.Samples) for examples of how to build modular and multi-tenant apps using just the Orchard Core Framework without any of its CMS-specific features.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="bba0b-426">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="bba0b-426">Framework-provided services</span></span>

<span data-ttu-id="bba0b-427">Die `Startup.ConfigureServices`-Methode registriert Dienste, die von der App verwendet werden, einschließlich Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="bba0b-427">The `Startup.ConfigureServices` method registers services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="bba0b-428">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="bba0b-428">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="bba0b-429">Für Apps, die auf ASP.NET Core-Vorlagen basieren, registriert das Framework mehr als 250 Dienste.</span><span class="sxs-lookup"><span data-stu-id="bba0b-429">For apps based on the ASP.NET Core templates, the framework registers more than 250 services.</span></span> 

<span data-ttu-id="bba0b-430">In der folgenden Tabelle finden Sie eine kleine Stichprobe der vom Framework registrierten Dienste:</span><span class="sxs-lookup"><span data-stu-id="bba0b-430">The following table lists a small sample of these framework-registered services:</span></span>

| <span data-ttu-id="bba0b-431">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="bba0b-431">Service Type</span></span>                                                                                    | <span data-ttu-id="bba0b-432">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="bba0b-432">Lifetime</span></span>  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="bba0b-433">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-433">Transient</span></span> |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | <span data-ttu-id="bba0b-434">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-434">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | <span data-ttu-id="bba0b-435">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-435">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | <span data-ttu-id="bba0b-436">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-436">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | <span data-ttu-id="bba0b-437">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-437">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | <span data-ttu-id="bba0b-438">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-438">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | <span data-ttu-id="bba0b-439">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-439">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | <span data-ttu-id="bba0b-440">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-440">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | <span data-ttu-id="bba0b-441">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-441">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | <span data-ttu-id="bba0b-442">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-442">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | <span data-ttu-id="bba0b-443">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-443">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | <span data-ttu-id="bba0b-444">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-444">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | <span data-ttu-id="bba0b-445">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-445">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | <span data-ttu-id="bba0b-446">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-446">Singleton</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="bba0b-447">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bba0b-447">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [<span data-ttu-id="bba0b-448">NDC-Konferenzmuster für die DI-App-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="bba0b-448">NDC Conference Patterns for DI app development</span></span>](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="bba0b-449">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bba0b-449">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="bba0b-450">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="bba0b-450">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="bba0b-451">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="bba0b-451">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="bba0b-452">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="bba0b-452">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="bba0b-453">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="bba0b-453">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bba0b-454">Von [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), und [Brandon Dahler](https://github.com/brandondahler)</span><span class="sxs-lookup"><span data-stu-id="bba0b-454">By [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), and [Brandon Dahler](https://github.com/brandondahler)</span></span>

<span data-ttu-id="bba0b-455">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-455">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="bba0b-456">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="bba0b-456">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="bba0b-457">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bba0b-457">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="bba0b-458">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="bba0b-458">Overview of dependency injection</span></span>

<span data-ttu-id="bba0b-459">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-459">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="bba0b-460">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="bba0b-460">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="bba0b-461">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-461">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="bba0b-462">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="bba0b-462">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="bba0b-463">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="bba0b-463">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="bba0b-464">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-464">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="bba0b-465">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-465">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="bba0b-466">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-466">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="bba0b-467">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-467">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="bba0b-468">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-468">This implementation is difficult to unit test.</span></span> <span data-ttu-id="bba0b-469">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-469">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="bba0b-470">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="bba0b-470">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="bba0b-471">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="bba0b-471">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="bba0b-472">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="bba0b-472">Registration of the dependency in a service container.</span></span> <span data-ttu-id="bba0b-473">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="bba0b-473">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="bba0b-474">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-474">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="bba0b-475">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-475">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="bba0b-476">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-476">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="bba0b-477">In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="bba0b-477">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="bba0b-478">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="bba0b-478">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="bba0b-479">`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="bba0b-479">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="bba0b-480">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-480">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="bba0b-481">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="bba0b-481">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="bba0b-482">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="bba0b-482">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="bba0b-483">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-483">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="bba0b-484">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-484">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="bba0b-485">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-485">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="bba0b-486">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-486">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="bba0b-487">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="bba0b-487">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="bba0b-488">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-488">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="bba0b-489">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="bba0b-489">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="bba0b-490">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-490">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="bba0b-491">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="bba0b-491">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="bba0b-492">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="bba0b-492">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="bba0b-493">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-493">We recommended that apps follow this convention.</span></span> <span data-ttu-id="bba0b-494">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="bba0b-494">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="bba0b-495">Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-495">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="bba0b-496">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-496">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="bba0b-497">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-497">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="bba0b-498">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="bba0b-498">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="bba0b-499">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="bba0b-499">Services injected into Startup</span></span>

<span data-ttu-id="bba0b-500">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-500">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="bba0b-501">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-501">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="bba0b-502">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="bba0b-502">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="bba0b-503">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="bba0b-503">Framework-provided services</span></span>

<span data-ttu-id="bba0b-504">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="bba0b-504">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="bba0b-505">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="bba0b-505">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="bba0b-506">Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-506">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="bba0b-507">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="bba0b-507">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="bba0b-508">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="bba0b-508">Service Type</span></span> | <span data-ttu-id="bba0b-509">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="bba0b-509">Lifetime</span></span> |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | <span data-ttu-id="bba0b-510">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-510">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | <span data-ttu-id="bba0b-511">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-511">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | <span data-ttu-id="bba0b-512">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-512">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | <span data-ttu-id="bba0b-513">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-513">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | <span data-ttu-id="bba0b-514">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-514">Transient</span></span> |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | <span data-ttu-id="bba0b-515">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-515">Singleton</span></span> |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | <span data-ttu-id="bba0b-516">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-516">Transient</span></span> |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | <span data-ttu-id="bba0b-517">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-517">Singleton</span></span> |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | <span data-ttu-id="bba0b-518">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-518">Singleton</span></span> |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | <span data-ttu-id="bba0b-519">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-519">Singleton</span></span> |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | <span data-ttu-id="bba0b-520">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-520">Transient</span></span> |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | <span data-ttu-id="bba0b-521">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-521">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | <span data-ttu-id="bba0b-522">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-522">Singleton</span></span> |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | <span data-ttu-id="bba0b-523">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-523">Singleton</span></span> |

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="bba0b-524">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="bba0b-524">Register additional services with extension methods</span></span>

<span data-ttu-id="bba0b-525">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="bba0b-525">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="bba0b-526">Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> zusätzliche Dienste zum Container hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-526">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="bba0b-527">Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="bba0b-527">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="bba0b-528">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="bba0b-528">Service lifetimes</span></span>

<span data-ttu-id="bba0b-529">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="bba0b-529">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="bba0b-530">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-530">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="bba0b-531">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="bba0b-531">Transient</span></span>

<span data-ttu-id="bba0b-532">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-532">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="bba0b-533">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-533">This lifetime works best for lightweight, stateless services.</span></span>

<span data-ttu-id="bba0b-534">In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-534">In apps that process requests, transient services are disposed at the end of the request.</span></span>

### <a name="scoped"></a><span data-ttu-id="bba0b-535">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="bba0b-535">Scoped</span></span>

<span data-ttu-id="bba0b-536">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-536">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

<span data-ttu-id="bba0b-537">In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-537">In apps that process requests, scoped services are disposed at the end of the request.</span></span>

> [!WARNING]
> <span data-ttu-id="bba0b-538">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-538">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="bba0b-539">Fügen Sie ihn nicht über [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-539">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="bba0b-540">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="bba0b-540">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="bba0b-541">Singleton</span><span class="sxs-lookup"><span data-stu-id="bba0b-541">Singleton</span></span>

<span data-ttu-id="bba0b-542">Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="bba0b-542">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="bba0b-543">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="bba0b-543">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="bba0b-544">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-544">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="bba0b-545">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="bba0b-545">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

<span data-ttu-id="bba0b-546">In Apps, die Anforderungen verarbeiten, werden Singleton-Dienste verworfen, wenn der <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren der App verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-546">In apps that process requests, singleton services are disposed when the <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> is disposed at app shutdown.</span></span>

> [!WARNING]
> <span data-ttu-id="bba0b-547">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-547">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="bba0b-548">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="bba0b-548">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="bba0b-549">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="bba0b-549">Service registration methods</span></span>

<span data-ttu-id="bba0b-550">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="bba0b-550">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="bba0b-551">Methode</span><span class="sxs-lookup"><span data-stu-id="bba0b-551">Method</span></span> | <span data-ttu-id="bba0b-552">Automatische</span><span class="sxs-lookup"><span data-stu-id="bba0b-552">Automatic</span></span><br><span data-ttu-id="bba0b-553">Objekt</span><span class="sxs-lookup"><span data-stu-id="bba0b-553">object</span></span><br><span data-ttu-id="bba0b-554">bereinigung</span><span class="sxs-lookup"><span data-stu-id="bba0b-554">disposal</span></span> | <span data-ttu-id="bba0b-555">Mehrere</span><span class="sxs-lookup"><span data-stu-id="bba0b-555">Multiple</span></span><br><span data-ttu-id="bba0b-556">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="bba0b-556">implementations</span></span> | <span data-ttu-id="bba0b-557">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="bba0b-557">Pass args</span></span> |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br><span data-ttu-id="bba0b-558">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bba0b-558">Example:</span></span><br>`services.AddSingleton<IMyDep, MyDep>();` | <span data-ttu-id="bba0b-559">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-559">Yes</span></span> | <span data-ttu-id="bba0b-560">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-560">Yes</span></span> | <span data-ttu-id="bba0b-561">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-561">No</span></span> |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br><span data-ttu-id="bba0b-562">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="bba0b-562">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | <span data-ttu-id="bba0b-563">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-563">Yes</span></span> | <span data-ttu-id="bba0b-564">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-564">Yes</span></span> | <span data-ttu-id="bba0b-565">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-565">Yes</span></span> |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br><span data-ttu-id="bba0b-566">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bba0b-566">Example:</span></span><br>`services.AddSingleton<MyDep>();` | <span data-ttu-id="bba0b-567">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-567">Yes</span></span> | <span data-ttu-id="bba0b-568">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-568">No</span></span> | <span data-ttu-id="bba0b-569">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-569">No</span></span> |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br><span data-ttu-id="bba0b-570">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="bba0b-570">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | <span data-ttu-id="bba0b-571">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-571">No</span></span> | <span data-ttu-id="bba0b-572">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-572">Yes</span></span> | <span data-ttu-id="bba0b-573">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-573">Yes</span></span> |
| `AddSingleton(new {IMPLEMENTATION})`<br><span data-ttu-id="bba0b-574">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="bba0b-574">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | <span data-ttu-id="bba0b-575">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-575">No</span></span> | <span data-ttu-id="bba0b-576">Nein</span><span class="sxs-lookup"><span data-stu-id="bba0b-576">No</span></span> | <span data-ttu-id="bba0b-577">Ja</span><span class="sxs-lookup"><span data-stu-id="bba0b-577">Yes</span></span> |

<span data-ttu-id="bba0b-578">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="bba0b-578">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="bba0b-579">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="bba0b-579">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="bba0b-580">`TryAdd{LIFETIME}`-Methoden registrieren den Dienst nur, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-580">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="bba0b-581">Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-581">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="bba0b-582">Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:</span><span class="sxs-lookup"><span data-stu-id="bba0b-582">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="bba0b-583">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="bba0b-583">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="bba0b-584">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-584">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="bba0b-585">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="bba0b-585">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="bba0b-586">Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="bba0b-586">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="bba0b-587">Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-587">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="bba0b-588">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-588">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="bba0b-589">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-589">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="bba0b-590">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="bba0b-590">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="bba0b-591">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="bba0b-591">Constructor injection behavior</span></span>

<span data-ttu-id="bba0b-592">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-592">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="bba0b-593"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Lässt die Erstellung von Objekten ohne Dienstregistrierung im Dependency-Injection-Container zu</span><span class="sxs-lookup"><span data-stu-id="bba0b-593"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="bba0b-594">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-594">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="bba0b-595">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-595">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="bba0b-596">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="bba0b-596">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="bba0b-597">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-597">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="bba0b-598">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="bba0b-598">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="bba0b-599">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="bba0b-599">Entity Framework contexts</span></span>

<span data-ttu-id="bba0b-600">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-600">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="bba0b-601">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-601">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="bba0b-602">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-602">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="bba0b-603">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="bba0b-603">Lifetime and registration options</span></span>

<span data-ttu-id="bba0b-604">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-604">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="bba0b-605">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="bba0b-605">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="bba0b-606">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-606">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="bba0b-607">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="bba0b-607">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="bba0b-608">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="bba0b-608">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="bba0b-609">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="bba0b-609">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="bba0b-610">Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-610">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="bba0b-611">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-611">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="bba0b-612">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="bba0b-612">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="bba0b-613">Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung.</span><span class="sxs-lookup"><span data-stu-id="bba0b-613">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="bba0b-614">Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-614">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="bba0b-615">Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="bba0b-615">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="bba0b-616">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="bba0b-616">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="bba0b-617">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="bba0b-617">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="bba0b-618">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="bba0b-618">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="bba0b-619">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="bba0b-619">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="bba0b-620">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="bba0b-620">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="bba0b-621">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="bba0b-621">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="bba0b-622">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="bba0b-622">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="bba0b-623">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="bba0b-623">**First request:**</span></span>

<span data-ttu-id="bba0b-624">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="bba0b-624">Controller operations:</span></span>

<span data-ttu-id="bba0b-625">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="bba0b-625">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="bba0b-626">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="bba0b-626">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="bba0b-627">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="bba0b-627">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="bba0b-628">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="bba0b-628">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="bba0b-629">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="bba0b-629">`OperationService` operations:</span></span>

<span data-ttu-id="bba0b-630">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="bba0b-630">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="bba0b-631">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="bba0b-631">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="bba0b-632">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="bba0b-632">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="bba0b-633">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="bba0b-633">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="bba0b-634">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="bba0b-634">**Second request:**</span></span>

<span data-ttu-id="bba0b-635">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="bba0b-635">Controller operations:</span></span>

<span data-ttu-id="bba0b-636">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="bba0b-636">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="bba0b-637">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="bba0b-637">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="bba0b-638">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="bba0b-638">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="bba0b-639">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="bba0b-639">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="bba0b-640">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="bba0b-640">`OperationService` operations:</span></span>

<span data-ttu-id="bba0b-641">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="bba0b-641">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="bba0b-642">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="bba0b-642">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="bba0b-643">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="bba0b-643">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="bba0b-644">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="bba0b-644">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="bba0b-645">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="bba0b-645">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="bba0b-646">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="bba0b-646">*Transient* objects are always different.</span></span> <span data-ttu-id="bba0b-647">Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="bba0b-647">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="bba0b-648">Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-648">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="bba0b-649">*Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="bba0b-649">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="bba0b-650">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="bba0b-650">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="bba0b-651">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="bba0b-651">Call services from main</span></span>

<span data-ttu-id="bba0b-652">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-652">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="bba0b-653">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-653">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="bba0b-654">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="bba0b-654">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="bba0b-655">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="bba0b-655">Scope validation</span></span>

<span data-ttu-id="bba0b-656">Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="bba0b-656">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="bba0b-657">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="bba0b-657">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="bba0b-658">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="bba0b-658">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="bba0b-659">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-659">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="bba0b-660">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-660">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="bba0b-661">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="bba0b-661">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="bba0b-662">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-662">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="bba0b-663">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-663">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="bba0b-664">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="bba0b-664">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>   

## <a name="request-services"></a><span data-ttu-id="bba0b-665">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="bba0b-665">Request Services</span></span>

<span data-ttu-id="bba0b-666">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="bba0b-666">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="bba0b-667">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-667">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="bba0b-668">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="bba0b-668">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="bba0b-669">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-669">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="bba0b-670">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-670">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="bba0b-671">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="bba0b-671">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="bba0b-672">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="bba0b-672">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="bba0b-673">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="bba0b-673">Design services for dependency injection</span></span>

<span data-ttu-id="bba0b-674">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="bba0b-674">Best practices are to:</span></span>

* <span data-ttu-id="bba0b-675">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="bba0b-675">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="bba0b-676">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="bba0b-676">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="bba0b-677">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="bba0b-677">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="bba0b-678">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="bba0b-678">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="bba0b-679">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="bba0b-679">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="bba0b-680">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="bba0b-680">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="bba0b-681">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-681">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="bba0b-682">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="bba0b-682">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="bba0b-683">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-683">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="bba0b-684">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="bba0b-684">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="bba0b-685">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="bba0b-685">Disposal of services</span></span>

<span data-ttu-id="bba0b-686">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="bba0b-686">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="bba0b-687">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="bba0b-687">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="bba0b-688">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="bba0b-688">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="bba0b-689">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bba0b-689">In the following example:</span></span>

* <span data-ttu-id="bba0b-690">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-690">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="bba0b-691">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="bba0b-691">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="bba0b-692">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="bba0b-692">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="bba0b-693">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="bba0b-693">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a><span data-ttu-id="bba0b-694">IDisposable-Anleitung für vorübergehende and freigegebene Instanzen</span><span class="sxs-lookup"><span data-stu-id="bba0b-694">IDisposable guidance for Transient and shared instances</span></span>

#### <a name="transient-limited-lifetime"></a><span data-ttu-id="bba0b-695">Vorübergehende, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="bba0b-695">Transient, limited lifetime</span></span>

<span data-ttu-id="bba0b-696">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="bba0b-696">**Scenario**</span></span>

<span data-ttu-id="bba0b-697">Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:</span><span class="sxs-lookup"><span data-stu-id="bba0b-697">The app requires an <xref:System.IDisposable> instance with a transient lifetime for either of the following scenarios:</span></span>

* <span data-ttu-id="bba0b-698">Die-Instanz wird im Stammbereich aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="bba0b-698">The instance is resolved in the root scope.</span></span>
* <span data-ttu-id="bba0b-699">Die Instanz sollte verworfen werden, bevor der Bereich endet.</span><span class="sxs-lookup"><span data-stu-id="bba0b-699">The instance should be disposed before the scope ends.</span></span>

<span data-ttu-id="bba0b-700">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="bba0b-700">**Solution**</span></span>

<span data-ttu-id="bba0b-701">Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-701">Use the factory pattern to create an instance outside of the parent scope.</span></span> <span data-ttu-id="bba0b-702">In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft.</span><span class="sxs-lookup"><span data-stu-id="bba0b-702">In this situation, the app would generally have a `Create` method that calls the final type's constructor directly.</span></span> <span data-ttu-id="bba0b-703">Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="bba0b-703">If the final type has other dependencies, the factory can:</span></span>

* <span data-ttu-id="bba0b-704">Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-704">Receive an <xref:System.IServiceProvider> in its constructor.</span></span>
* <span data-ttu-id="bba0b-705">Sie kann <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> verwenden, um eine Instanz außerhalb des Containers zu instanziieren und dabei den Container für die Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-705">Use <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> to instantiate the instance outside the container, while using the container for its dependencies.</span></span>

#### <a name="shared-instance-limited-lifetime"></a><span data-ttu-id="bba0b-706">Freigegebene Instanz, eingeschränkte Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="bba0b-706">Shared Instance, limited lifetime</span></span>

<span data-ttu-id="bba0b-707">**Szenario**</span><span class="sxs-lookup"><span data-stu-id="bba0b-707">**Scenario**</span></span>

<span data-ttu-id="bba0b-708">Für die App ist eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste erforderlich, die <xref:System.IDisposable> sollte jedoch eine begrenzte Lebensdauer haben.</span><span class="sxs-lookup"><span data-stu-id="bba0b-708">The app requires a shared <xref:System.IDisposable> instance across multiple services, but the <xref:System.IDisposable> should have a limited lifetime.</span></span>

<span data-ttu-id="bba0b-709">**Lösung**</span><span class="sxs-lookup"><span data-stu-id="bba0b-709">**Solution**</span></span>

<span data-ttu-id="bba0b-710">Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="bba0b-710">Register the instance with a Scoped lifetime.</span></span> <span data-ttu-id="bba0b-711">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> für den Start und zum Erstellen einer neuen <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span><span class="sxs-lookup"><span data-stu-id="bba0b-711">Use <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> to start and create a new <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>.</span></span> <span data-ttu-id="bba0b-712">Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-712">Use the scope's <xref:System.IServiceProvider> to get required services.</span></span> <span data-ttu-id="bba0b-713">Löschen Sie den Bereich, wenn die Lebensdauer beendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="bba0b-713">Dispose the scope when the lifetime should end.</span></span>

#### <a name="general-guidelines"></a><span data-ttu-id="bba0b-714">Allgemeine Richtlinien</span><span class="sxs-lookup"><span data-stu-id="bba0b-714">General Guidelines</span></span>

* <span data-ttu-id="bba0b-715">Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einem vorübergehenden Gültigkeitsbereich.</span><span class="sxs-lookup"><span data-stu-id="bba0b-715">Don't register <xref:System.IDisposable> instances with a Transient scope.</span></span> <span data-ttu-id="bba0b-716">Verwenden Sie stattdessen das Factorymuster.</span><span class="sxs-lookup"><span data-stu-id="bba0b-716">Use the factory pattern instead.</span></span>
* <span data-ttu-id="bba0b-717">Lösen Sie keine vorübergehenden oder bereichsbezogenen <xref:System.IDisposable>-Instanzen im Stammbereich auf.</span><span class="sxs-lookup"><span data-stu-id="bba0b-717">Don't resolve Transient or Scoped <xref:System.IDisposable> instances in the root scope.</span></span> <span data-ttu-id="bba0b-718">Die einzige allgemeine Ausnahme gilt, wenn die App die <xref:System.IServiceProvider>-Instanz erstellt bzw. erneut erstellt und freigibt, was kein ideales Muster ist.</span><span class="sxs-lookup"><span data-stu-id="bba0b-718">The only general exception is when the app creates/recreates and disposes the <xref:System.IServiceProvider>, which isn't an ideal pattern.</span></span>
* <span data-ttu-id="bba0b-719">Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert.</span><span class="sxs-lookup"><span data-stu-id="bba0b-719">Receiving an <xref:System.IDisposable> dependency via DI doesn't require that the receiver implement <xref:System.IDisposable> itself.</span></span> <span data-ttu-id="bba0b-720">Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-720">The receiver of the <xref:System.IDisposable> dependency shouldn't call <xref:System.IDisposable.Dispose%2A> on that dependency.</span></span>
* <span data-ttu-id="bba0b-721">Bereiche sollten verwendet werden, um die Lebensdauer von Diensten zu steuern.</span><span class="sxs-lookup"><span data-stu-id="bba0b-721">Scopes should be used to control lifetimes of services.</span></span> <span data-ttu-id="bba0b-722">Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-722">Scopes aren't hierarchical, and there's no special connection among scopes.</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="bba0b-723">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="bba0b-723">Default service container replacement</span></span>

<span data-ttu-id="bba0b-724">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-724">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="bba0b-725">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="bba0b-725">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="bba0b-726">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="bba0b-726">Property injection</span></span>
* <span data-ttu-id="bba0b-727">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="bba0b-727">Injection based on name</span></span>
* <span data-ttu-id="bba0b-728">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="bba0b-728">Child containers</span></span>
* <span data-ttu-id="bba0b-729">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="bba0b-729">Custom lifetime management</span></span>
* <span data-ttu-id="bba0b-730">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="bba0b-730">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="bba0b-731">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="bba0b-731">Convention-based registration</span></span>

<span data-ttu-id="bba0b-732">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="bba0b-732">The following third-party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="bba0b-733">Autofac</span><span class="sxs-lookup"><span data-stu-id="bba0b-733">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="bba0b-734">DryIoc</span><span class="sxs-lookup"><span data-stu-id="bba0b-734">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="bba0b-735">Grace</span><span class="sxs-lookup"><span data-stu-id="bba0b-735">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="bba0b-736">LightInject</span><span class="sxs-lookup"><span data-stu-id="bba0b-736">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="bba0b-737">Lamar</span><span class="sxs-lookup"><span data-stu-id="bba0b-737">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="bba0b-738">Stashbox</span><span class="sxs-lookup"><span data-stu-id="bba0b-738">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="bba0b-739">Unity</span><span class="sxs-lookup"><span data-stu-id="bba0b-739">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="bba0b-740">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="bba0b-740">Thread safety</span></span>

<span data-ttu-id="bba0b-741">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="bba0b-741">Create thread-safe singleton services.</span></span> <span data-ttu-id="bba0b-742">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="bba0b-742">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="bba0b-743">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="bba0b-743">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="bba0b-744">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="bba0b-744">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="bba0b-745">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="bba0b-745">Recommendations</span></span>

* <span data-ttu-id="bba0b-746">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-746">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="bba0b-747">C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="bba0b-747">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="bba0b-748">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="bba0b-748">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="bba0b-749">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-749">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="bba0b-750">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-750">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="bba0b-751">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-751">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="bba0b-752">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="bba0b-752">It's better to request the actual item via DI.</span></span>
* <span data-ttu-id="bba0b-753">Vermeiden Sie statischen Zugriff auf Dienste.</span><span class="sxs-lookup"><span data-stu-id="bba0b-753">Avoid static access to services.</span></span> <span data-ttu-id="bba0b-754">Vermeiden Sie statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle.</span><span class="sxs-lookup"><span data-stu-id="bba0b-754">For example, avoid statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere.</span></span>

* <span data-ttu-id="bba0b-755">Vermeiden Sie die Verwendung des *Dienstlocatormusters*, da dieses [Steuerungsumkehrungsstrategien](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) vermischt.</span><span class="sxs-lookup"><span data-stu-id="bba0b-755">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>
  * <span data-ttu-id="bba0b-756">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Abhängigkeitsinjektion verwenden können:</span><span class="sxs-lookup"><span data-stu-id="bba0b-756">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="bba0b-757">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="bba0b-757">**Incorrect:**</span></span>

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
   
    <span data-ttu-id="bba0b-758">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="bba0b-758">**Correct**:</span></span>

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

* <span data-ttu-id="bba0b-759">Vermeiden Sie die Injektion von Factorys, die Abhängigkeiten zur Laufzeit mit <xref:System.IServiceProvider.GetService*> auflösen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-759">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>
* <span data-ttu-id="bba0b-760">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="bba0b-760">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="bba0b-761">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="bba0b-761">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="bba0b-762">Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="bba0b-762">Exceptions are rare, mostly special cases within the framework itself.</span></span>

<span data-ttu-id="bba0b-763">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="bba0b-763">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="bba0b-764">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="bba0b-764">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bba0b-765">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bba0b-765">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="bba0b-766">Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bba0b-766">Four ways to dispose IDisposables in ASP.NET Core</span></span>](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [<span data-ttu-id="bba0b-767">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="bba0b-767">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [<span data-ttu-id="bba0b-768">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="bba0b-768">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="bba0b-769">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="bba0b-769">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="bba0b-770">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="bba0b-770">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
