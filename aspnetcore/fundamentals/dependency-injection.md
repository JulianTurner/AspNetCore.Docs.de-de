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
ms.openlocfilehash: 2d002e075f9d57654589b540e522307c363d9660
ms.sourcegitcommit: 4cce99cbd44372fd4575e8da8c0f4345949f4d9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89153544"
---
# <a name="dependency-injection-in-aspnet-core"></a>Dependency Injection in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Von [Kirk Larkin](https://twitter.com/serpent5), [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com) und [Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.

Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.

Weitere Informationen zur Dependency Injection für Optionen finden Sie unter <xref:fundamentals/configuration/options>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Übersicht über Abhängigkeitsinjektion

Eine *Abhängigkeit* ist ein Objekt, von dem ein anderes Objekt abhängig ist. Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen abhängig sind:

```csharp
public class MyDependency
{
    public void WriteMessage(string message)
    {
        Console.WriteLine($"MyDependency.WriteMessage called. Message: {message}");
    }
}
```

Eine Klasse kann eine Instanz der `MyDependency`-Klasse erstellen, um die `WriteMessage`-Methode zu nutzen. Im folgenden Beispiel ist die `MyDependency`-Klasse eine Abhängigkeit der `IndexModel`-Klasse:

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

Die Klasse erstellt die `MyDependency`-Klasse und weist eine direkte Abhängigkeit von dieser auf. Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:

* Die `IndexModel`-Klasse muss geändert werden, um `MyDependency` durch eine andere Implementierung zu ersetzen.
* Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese ebenfalls von der `IndexModel`-Klasse konfiguriert werden. In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.
* Diese Implementierung ist nicht für Komponententests geeignet. Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.

Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:

* Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.
* Registrierung der Abhängigkeit in einem Dienstcontainer. ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit. Dienste werden üblicherweise in der `Startup.ConfigureServices`-Methode der App registriert.
* Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird. Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.

In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle die `WriteMessage`-Methode:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

Die Beispiel-App registriert den `IMyDependency`-Dienst mit dem konkreten Typ `MyDependency`. Die <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A>-Methode registriert den Dienst mit der Lebensdauer einer einzelnen Anforderung. Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency.cs?name=snippet1)]

In der Beispiel-App wird der `IMyDependency`-Dienst angefordert und zum Aufrufen der `WriteMessage`-Methode verwendet:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index2.cshtml.cs?name=snippet1)]

Bei Verwendung des Dependency-Injection-Musters geht der Controller wie folgt vor:

* Er verwendet nicht den konkreten Typ `MyDependency`, nur die von diesem implementierte `IMyDependency`-Schnittstelle. Dadurch kann die vom Controller verwendete Implementierung einfacher geändert werden, ohne den Controller selbst zu bearbeiten.
* Er erstellt keine Instanz von `MyDependency`. Diese wird vom DI-Container erstellt.

Die Implementierung der `IMyDependency`-Schnittstelle kann mithilfe der integrierten Protokollierungs-API verbessert werden:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet2)]

Die aktualisierte `ConfigureServices`-Methode registriert die neue `IMyDependency`-Implementierung:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupMyDependency2.cs?name=snippet1)]

`MyDependency2` hängt von der <xref:Microsoft.Extensions.Logging.ILogger%601>-Schnittstelle ab, die im Konstruktor angefordert wird. `ILogger<TCategoryName>` ist ein [vom Framework bereitgestellter Dienst](#framework-provided-services).

Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet. Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an. Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück. Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.

Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch nicht mehr jeder [(generische) konstruierte Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) registriert werden muss:

Im Dependency-Injection-Kontext ist ein Dienst:

* in der Regel ein Objekt, das einen Dienst für andere Objekte bereitstellt, z. B. den `IMyDependency`-Dienst.
* kein Webdienst, obwohl ein Dienst einen Webdienst verwenden kann

Das Framework bietet eine stabiles [Protokollierungssystem](xref:fundamentals/logging/index). Die in den vorherigen Beispielen veranschaulichten `IMyDependency`-Implementierungen wurden geschrieben, um die grundlegende Dependency Injection zu demonstrieren, nicht zum Implementieren der Protokollierung. Die meisten Apps müssen keine Protokollierungen schreiben. Der folgende Code veranschaulicht die Verwendung der Standardprotokollierung, bei der keine Dienste in `ConfigureServices` registriert werden müssen:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/About.cshtml.cs?name=snippet)]

Bei Verwendung des vorangehenden Codes muss `ConfigureServices` nicht aktualisiert werden, weil die [Protokollierung](xref:fundamentals/logging/index) vom Framework bereitgestellt wird.

## <a name="services-injected-into-startup"></a>In den Start eingefügte Dienste

Dienste können in den `Startup`-Konstruktor und die `Startup.Configure`-Methode eingefügt werden.

Nur die folgenden Dienste können in den `Startup`-Konstruktor eingefügt werden, wenn der generische Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) verwendet wird:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Jeder Dienst, der mit dem DI-Container registriert wurde, kann in die `Startup.Configure`-Methode eingefügt werden:

```csharp
public void Configure(IApplicationBuilder app, ILogger<Startup> logger)
{
    ...
}
```

Weitere Informationen finden Sie unter <xref:fundamentals/startup> und unter [Zugriffskonfiguration beim Start](xref:fundamentals/configuration/index#access-configuration-in-startup).

## <a name="register-groups-of-services-with-extension-methods"></a>Registrieren von Dienstgruppen mit Erweiterungsmethoden

Das ASP.NET Core-Framework verwendet eine Konvention zum Registrieren einer Gruppe verwandter Dienste. Die Konvention besteht darin, eine einzelne `Add{GROUP_NAME}`-Erweiterungsmethode zum Registrieren aller Dienste zu verwenden, die von einem Frameworkfeature benötigt werden. Beispielsweise registriert die Erweiterungsmethode <Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers> die Dienste, die für MVC-Controller erforderlich sind.

Der folgende Code wird von der Razor Pages-Vorlage auf Grundlage einzelner Benutzerkonten generiert. Er veranschaulicht, wie mit den Erweiterungsmethoden <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity%2A> zusätzliche Dienste zum Container hinzugefügt werden können:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/StartupEF.cs?name=snippet)]

[!INCLUDE[](~/includes/combine-di.md)]

## <a name="service-lifetimes"></a>Dienstlebensdauer

Dienste können mit einer den folgenden Lebensdaueroptionen registriert werden:

* Transient (vorübergehend)
* Bereichsbezogen
* Singleton

In den folgenden Abschnitten werden die einzelnen genannten Lebensdaueroptionen beschrieben. Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus. 

### <a name="transient"></a>Transient (vorübergehend)

Kurzlebige Dienste werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt. Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet. Vorübergehende Dienste werden mit <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient%2A> registriert.

In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.

### <a name="scoped"></a>Bereichsbezogen

Dienste mit bereichsbezogener Lebensdauer werden einmal pro Clientanforderung (-verbindung) erstellt. Bereichsbezogene Dienste werden mit <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped%2A> registriert.

In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.

Wenn Sie Entity Framework Core verwenden, registriert die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterungsmethode standardmäßig `DbContext`-Typen mit einer begrenzten Lebensdauer.

Lösen Sie einen bereichsbezogenen Dienst ***nicht*** über ein Singleton auf. Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf. Folgendes ist zulässig:

* das Auflösen eines Singletondiensts aus einem bereichsbezogenen oder temporären Diensts.
* das Auflösen eines bereichsbezogenen Diensts aus einem anderen bereichsbezogenen oder temporären Dienst

Standardmäßig wird in der Entwicklungsumgebung eine Ausnahme ausgelöst, wenn ein Dienst von einem anderen Dienst mit längerer Lebensdauer aufgelöst wird. Weitere Informationen finden Sie unter [Bereichsvalidierung](#sv).

Zum Verwenden bereichsbezogener Dienste in Middleware, verwenden Sie einen der folgenden Ansätze:

* Fügen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode der Middleware ein. Bei Verwendung der [Constructor Injection](xref:mvc/controllers/dependency-injection#constructor-injection) wird eine Runtimeausnahme ausgelöst, weil dem bereichsbezogenen Dienst das Verhalten eines Singletons aufgezwungen wird. Im Beispiel des Abschnitts [Lebensdauer und Registrierungsoptionen](#lifetime-and-registration-options) wird der `InvokeAsync`-Ansatz veranschaulicht.
* Verwenden Sie [factorybezogene Middleware](xref:fundamentals/middleware/extensibility). Middleware, die mit diesem Ansatz registriert wurde, wird pro Clientanforderung (Verbindung) aktiviert, wodurch bereichsbezogene Dienste in die `InvokeAsync`-Methode der Middleware eingefügt werden können.

Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Dienste mit Singletonlebensdauer werden erstellt:

* wenn sie zum ersten Mal angefordert werden
* vom Entwickler, wenn eine Implementierungsinstanz direkt im Container bereitgestellt wird (selten benötigter Ansatz)

Jeder nachfolgenden Anforderung verwendet die gleiche Instanz. Wenn für die App Singletonverhalten erforderlich ist, sollte der Dienstcontainer die Dienstlebensdauer verwalten. Wenn Sie das Singletonentwurfsmuster implementieren, sollten Sie keinen Code zum Löschen des Singleton angeben. Dienste sollten nicht durch den Code gelöscht werden können, der den Dienst aus einem Container aufgelöst hat. Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton automatisch vom Container verworfen.

Registrieren Sie Singletondienste mit <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A>. Singletondienste müssen threadsicher sein und werden häufig in zustandslosen Diensten verwendet.

In Apps, die Anforderungen verarbeiten, werden Singletondienste gelöscht, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren gelöscht wird. Da der Arbeitsspeicher erst freigegeben wird, wenn die App heruntergefahren wird, müssen Sie den Arbeitsspeicherverbrauch eines Singletons berücksichtigen.

> [!WARNING]
> Lösen Sie einen bereichsbezogenen Dienst ***nicht*** über ein Singleton auf. Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf. Sie können einen Singletondienst aus einem bereichsbezogenen oder temporären Dienst auflösen.

## <a name="service-registration-methods"></a>Dienstregistrierungsmethoden

Das Framework stellt Erweiterungsmethoden für die Dienstregistrierung bereit, die in bestimmten Szenarios hilfreich sind:

<!-- Review: Auto disposal at end of app lifetime is not what you think of auto disposal  -->

| Methode                                                                                                                                                                              | Automatische<br>Objekt<br>bereinigung | Mehrere<br>Implementierungen | Argumentübergabe |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------:|:---------------------------:|:---------:|
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Beispiel:<br>`services.AddSingleton<IMyDep, MyDep>();`                                                                             | Ja                             | Ja                         | Nein        |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Beispiele:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep(99));` | Ja                             | Ja                         | Ja       |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Beispiel:<br>`services.AddSingleton<MyDep>();`                                                                                                | Ja                             | Nein                          | Nein        |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Beispiele:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep(99));`                    | Nein                              | Ja                         | Ja       |
| `AddSingleton(new {IMPLEMENTATION})`<br>Beispiele:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep(99));`                                               | Nein                              | Nein                          | Ja       |

Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services). Es ist üblich, mehrere Implementierungen zu verwenden, [wenn Typen zu Testzecken simuliert werden](xref:test/integration-tests#inject-mock-services).

Das Framework stellt außerdem `TryAdd{LIFETIME}`-Erweiterungsmethoden bereit, die den Dienst nur registrieren, wenn noch keine Implementierung registriert ist.

Im folgenden Beispiel registriert der `AddSingleton`-Aufruf `MyDependency` als Implementierung für `IMyDependency`. Der Aufruf von `TryAddSingleton` hat keine Auswirkung, weil `IMyDependency` bereits eine registrierte Implementierung aufweist:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Weitere Informationen finden Sie unter

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped%2A>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton%2A>

Die [TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable%2A)-Methoden registrieren den Dienst nur, wenn noch keine Implementierung *desselben Typs* vorhanden ist. Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst. Beim Registrieren von Diensten sollte der Entwickler nur dann eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde. Im Allgemeinen verwenden Bibliotheksautoren `TryAddEnumerable`, um zu vermeiden, dass mehrere Kopien einer Implementierung im Container registriert werden.

Im folgenden Beispiel registriert der erste `TryAddEnumerable`-Aufruf `MyDependency` als Implementierung für `IMyDependency1`. Der zweite Aufruf registriert `MyDependency` für `IMyDependency2`. Der dritte Aufruf hat keine Auswirkungen, da `IMyDependency1` bereits eine registrierte Implementierung von `MyDependency` aufweist:

```csharp
public interface IMyDependency1 { }
public interface IMyDependency2 { }

public class MyDependency : IMyDependency1, IMyDependency2 { }

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency2, MyDependency>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDependency1, MyDependency>());
```

Bei der Dienstregistrierung ist die Reihenfolge unerheblich, wenn mehrere Implementierungen desselben Typs registriert werden.

`IServiceCollection` ist eine Sammlung von <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>-Objekten. Im folgenden Beispiel wird gezeigt, wie ein Dienst durch Erstellen und Hinzufügen von `ServiceDescriptor` registriert wird:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup5.cs?name=snippet)]

Die integrierten `Add{LIFETIME}`-Methoden verwenden denselben Ansatz. Ein Beispiel finden Sie im [Quellcode für AddScoped](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237).

### <a name="constructor-injection-behavior"></a>Verhalten von Constructor Injection

Dienste können mithilfe der folgenden Ansätze aufgelöst werden:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>:
  * erstellt Objekte, die nicht im Container registriert sind.
  * wird mit Frameworkfeatures wie [Taghilfsprogrammen](xref:mvc/views/tag-helpers/intro), MVC-Controllern und [Modellbindungen](xref:mvc/models/model-binding) verwendet

Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.

Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.

Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist. Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.

## <a name="entity-framework-contexts"></a>Entity Framework-Kontexte

Entity Framework-Kontexte werden einem Dienstcontainer standardmäßig mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden. Legen Sie die Lebensdauer mithilfe einer <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Überladung fest, um eine andere Lebensdauer zu verwenden. Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer Lebensdauer verwenden, die kürzer als die Lebensdauer des Diensts ist.

## <a name="lifetime-and-registration-options"></a>Lebensdauer und Registrierungsoptionen

In den folgenden Schnittstellen, die einen Task als Vorgang mit einem Bezeichner (`OperationId`) darstellen, wird der Unterschied zwischen Dienstlebensdauern und ihren Registrierungsoptionen veranschaulicht. Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Die folgende `Operation`-Klasse implementiert alle vorangehenden Schnittstellen. Der `Operation`-Konstruktor generiert einen eindeutigen Bezeichner (GUID) und speichert die letzten 4 Zeichen in der `OperationId`-Eigenschaft:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<!--
An `OperationService` is registered that depends on each of the other `Operation` types. When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.

* When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`. `OperationService` receives a new instance of the `IOperationTransient` class. The new instance yields a different `OperationId`.
* When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request. Across client requests, both services share a different `OperationId` value.
* When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]
-->

Die `Startup.ConfigureServices`-Methode erstellt mehrere Registrierungen der `Operation`-Klasse entsprechend der benannten Lebensdauern:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup2.cs?name=snippet1)]

Die Beispiel-App veranschaulicht die Objektlebensdauer sowohl in als auch zwischen Anforderungen. `IndexModel` und die Middleware fordern jede Art von `IOperation`-Typ an und protokollieren `OperationId` für jeden Typ:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1)]

Ähnlich wie `IndexModel` löst die Middleware dieselben Dienste auf:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet)]

Bereichsbezogene Dienste müssen in der `InvokeAsync`-Methode aufgelöst werden:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Middleware/MyMiddleware.cs?name=snippet2&highlight=2)]

Die Protokollierungsausgabe zeigt Folgendes an:

* Objekte vom Typ *Vorübergehend* sind immer unterschiedlich. Der vorübergehende `OperationId`-Wert unterscheidet sich in `IndexModel` und der Middleware.
* *Bereichsbezogene* Objekte sind für jede Anforderung identisch, aber unterscheiden sich für jede Anforderung.
* *Singletonobjekte* sind für jede Anforderung identisch.

Sie können "Logging:LogLevel:Microsoft:Error" in der Datei *appsettings.Development.json* festlegen, um die Protokollierungsausgabe zu reduzieren:

[!code-json[](dependency-injection/samples/3.x/DependencyInjectionSample/appsettings.Development.json?highlight=7)]

## <a name="call-services-from-main"></a>Abrufen von Diensten aus „Main“

Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen. Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.

Im folgenden Beispiel wird der Zugriff auf den bereichsbezogenen `IMyDependency`-Dienst und der Aufruf dessen `WriteMessage`-Methode in `Program.Main` veranschaulicht:

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Program.cs?name=snippet)]

<a name="sv"></a>

## <a name="scope-validation"></a>Bereichsvalidierung

Wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird und [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) aufruft, um den Host zu erstellen, stellt der Standarddienstanbieter sicher, dass:

* bereichsbezogene Dienste nicht vom Stammdienstanbieter aufgelöst werden.
* bereichsbezogene Dienste nicht in Singletons eingefügt werden.

Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> aufgerufen wird. Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App, wenn der Anbieter beim Start der App erstellt und beim Herunterfahren gelöscht wird.

Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat. Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer quasi auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App beendet wird. Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.

Weitere Informationen finden Sie unter [Bereichsvalidierung](xref:fundamentals/host/web-host#scope-validation).

## <a name="request-services"></a>Anfordern von Diensten

Die in einer ASP.NET Core-Anforderung verfügbaren Dienste werden über die Sammlung [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) zur Verfügung gestellt. Wenn Dienste innerhalb einer Anforderung angefordert werden, werden die Dienste und ihre Abhängigkeiten aus der `RequestServices`-Sammlung aufgelöst.

Das Framework erstellt einen Bereich pro Anforderung, und `RequestServices` stellt den bereichsbezogenen Dienstanbieter zur Verfügung. Alle bereichsbezogenen Dienste sind gültig, solange die Anforderung aktiv ist.

> [!NOTE]
> Sie sollten das Anfordern von Abhängigkeiten als Konstruktorparameter zum Auflösen von Diensten aus der `RequestServices`-Sammlung bevorzugen. Dies resultiert in Klassen, die einfacher getestet werden können.

## <a name="design-services-for-dependency-injection"></a>Entwerfen von Diensten für die Abhängigkeitsinjektion

Beachten Sie Folgendes beim Entwerfen von Diensten für Dependency Injection:

* Vermeiden Sie zustandsbehaftete statische Klassen und Member. Vermeiden Sie das Erstellen eines globalen Zustands, indem Sie Apps stattdessen zur Verwendung von Singletondiensten entwerfen.
* Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten. Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.
* Erstellen Sie kleine, gut gestaltete und einfach zu testende Dienste.

Wenn eine Klasse viele eingefügte Abhängigkeiten aufweist, ist dies möglicherweise ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Prinzip der einzelnen Verantwortung (SRP, Single Responsibility Principle)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) verstößt. Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Verantwortung in neue Klassen verschieben. Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.

### <a name="disposal-of-services"></a>Löschen von Diensten

Der Container ruft <xref:System.IDisposable.Dispose%2A> für die erstellten <xref:System.IDisposable>-Typen auf. Dienste, die aus dem Container aufgelöst werden, sollten nie vom Entwickler gelöscht werden. Wenn ein Typ oder eine Factory als Singleton registriert ist, wird das Singleton automatisch vom Container verworfen.

Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Services/Service1.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup.cs?name=snippet)]

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Pages/Index.cshtml.cs?name=snippet)]

In der Debuggingkonsole wird nach jeder Aktualisierung der Indexseite die folgende Ausgabe angezeigt:

```console
Service1: IndexModel.OnGet
Service2: IndexModel.OnGet
Service3: IndexModel.OnGet
Service1.Dispose
```

### <a name="services-not-created-by-the-service-container"></a>Nicht vom Dienstcontainer erstellte Dienste

Betrachten Sie folgenden Code:

[!code-csharp[](dependency-injection/samples/3.x/DIsample2/DIsample2/Startup2.cs?name=snippet)]

Für den Code oben gilt:

* werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.
* verwirft das Framework die Dienste nicht automatisch.
* Der Entwickler ist für das Löschen der Dienste verantwortlich.

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>IDisposable-Anleitung für vorübergehende and freigegebene Instanzen

#### <a name="transient-limited-lifetime"></a>Vorübergehende, eingeschränkte Lebensdauer

**Szenario**

Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:

* Die Instanz wird im Stammbereich (Stammcontainer) aufgelöst.
* Die Instanz sollte verworfen werden, bevor der Bereich endet.

**Lösung**

Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen. In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft. Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:

* Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.
* Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType>, um die Instanz außerhalb des Containers zu instanziieren, während Sie den Container für die Abhängigkeiten verwenden.

#### <a name="shared-instance-limited-lifetime"></a>Freigegebene Instanz (eingeschränkte Lebensdauer)

**Szenario**

Die App erfordert eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste, jedoch sollte die <xref:System.IDisposable>-Instanz eine begrenzte Lebensdauer aufweisen.

**Lösung**

Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer. Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType>, um eine neue <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>-Schnittstelle zu erstellen. Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen. Löschen Sie den Bereich, wenn er nicht mehr benötigt wird.

#### <a name="general-idisposable-guidelines"></a>Allgemeine Richtlinien für IDisposable

* Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einer vorübergehenden Lebensdauer. Verwenden Sie stattdessen das Factorymuster.
* Lösen Sie keine <xref:System.IDisposable>-Instanzen mit vorübergehender oder bereichsbezogener Lebensdauer im Stammbereich auf. Die einzige Ausnahme hierfür besteht in dem Fall, dass die App <xref:System.IServiceProvider> erstellt oder neu erstellt und löscht. Dieses Muster ist jedoch nicht ideal.
* Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert. Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.
* Verwenden Sie Bereiche, um die Lebensdauer von Diensten zu steuern. Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.

## <a name="default-service-container-replacement"></a>Ersetzen von Standarddienstcontainern

Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen. Die Verwendung der integrierten Container wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das nicht unterstützt wird, zum Beispiel:

* Eigenschaftsinjektion
* Auf Namen basierende Injektion
* Untergeordnete Container
* Benutzerdefinierte Verwaltung der Lebensdauer
* `Func<T>`-Unterstützung für die verzögerte Initialisierung
* Konventionsbasierte Registrierung

Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

## <a name="thread-safety"></a>Threadsicherheit

Erstellen Sie threadsichere Singleton-Dienste. Wenn ein Singletondienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.

Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton%2A), muss nicht threadsicher sein. Wie bei Konstruktoren vom Typ `static` erfolgt der Aufruf garantiert nur einmal über einen einzelnen Thread.

## <a name="recommendations"></a>Empfehlungen

* Die auf `async/await` und `Task` basierende Dienstauflösung wird nicht unterstützt. Da C# asynchrone Konstruktoren nicht unterstützt, verwenden Sie asynchrone Methoden, nachdem der Dienst synchron aufgelöst wurde.
* Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer. Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden. Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden. Gleichermaßen sollten Sie „Daten enthaltende“ Objekte vermeiden, die nur dafür vorhanden sind, den Zugriff auf ein anderes Objekt zuzulassen. Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.
* Vermeiden Sie statischen Zugriff auf Dienste. Vermeiden Sie beispielsweise das Erfassen von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) als statisches Feld oder als Eigenschaft zur Verwendung an einer anderen Stelle.
* DI-Factorys sollten schnell und synchron sein.
* Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*. Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService%2A> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können:

  **Falsch:**

    ![Falscher Code](dependency-injection/_static/bad.png)

  **Richtig**:

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
* Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst. Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).
* Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

<a name="ASP0000"></a>
* Vermeiden Sie Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider%2A> in `ConfigureServices`. `BuildServiceProvider` wird in der Regel aufgerufen, wenn der Entwickler einen Dienst in `ConfigureServices` auflösen möchte. Berücksichtigen Sie beispielsweise den Fall, wenn `LoginPath` aus der Konfiguration geladen wird. Vermeiden Sie den folgenden Ansatz:

  ![Ungültiger Code beim Aufruf von BuildServiceProvider](~/fundamentals/dependency-injection/_static/badcodeX.png)

  Wenn Sie auf die grüne Wellenlinie unter `services.BuildServiceProvider` auf der vorherigen Abbildung klicken würden, würde folgende ASP0000-Warnung angezeigt werden:

  > ASP0000: Der Aufruf von BuildServiceProvider aus dem Anwendungscode führt dazu, dass eine zusätzliche Kopie der Singletondienste erstellt wird. Verwenden Sie Alternativen wie Dependency-Injection-Dienste als Parameter für Configure.

  Durch den Aufruf von `BuildServiceProvider` wird ein zweiter Container erstellt, der fragmentierte Singletons und Verweise auf Objektgraphen für mehrere Container verursachen kann.

  Eine korrekte Methode zum Abrufen von `LoginPath` besteht darin, die integrierte Unterstützung von DI des Optionsmusters zu verwenden:

  [!code-csharp[](dependency-injection/samples/3.x/AntiPattern3/Startup.cs?name=snippet)]

* Löschbare temporäre Dienste werden vom Container für die Löschung erfasst. Dadurch kann es zu Arbeitsspeicherverlusten kommen, wenn diese vom obersten Container aufgelöst werden.
* Aktivieren Sie die Bereichsüberprüfung, um sicherzustellen, dass die App keine Singletons aufweist, die bereichsbezogene Dienste erfassen. Weitere Informationen finden Sie unter [Bereichsvalidierung](#scope-validation).

Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss. Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.

Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar. Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a>Empfohlene Muster für Mehrinstanzenfähigkeit in der Dependency Injection (DI)

[Orchard Core](https://github.com/OrchardCMS/OrchardCore) ist ein Anwendungsframework zum Erstellen modularer Anwendung mit mehreren Mandanten in ASP.NET Core. Weitere Informationen finden Sie in der [Orchard Core-Dokumentation](https://docs.orchardcore.net/en/dev/).

Beispiele zum Erstellen modularer Apps und Apps mit mehreren Mandanten nur mit dem Orchard Core-Framework und ohne den CMS-spezifischen Features finden Sie in den [Orchard Core-Beispielen](https://github.com/OrchardCMS/OrchardCore.Samples).

## <a name="framework-provided-services"></a>Von Frameworks bereitgestellte Dienste

Die `Startup.ConfigureServices`-Methode registriert Dienste, die von der App verwendet werden, einschließlich Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC. Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde. Für Apps, die auf ASP.NET Core-Vorlagen basieren, registriert das Framework mehr als 250 Dienste. 

In der folgenden Tabelle finden Sie eine kleine Stichprobe der vom Framework registrierten Dienste:

| Diensttyp                                                                                    | Lebensdauer  |
|-------------------------------------------------------------------------------------------------|-----------|
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient (vorübergehend) |
| <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>                                    | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>                                         | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName>                           | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName>                     | Transient (vorübergehend) |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName>                     | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName>                   | Transient (vorübergehend) |
| <xref:Microsoft.Extensions.Logging.ILogger%601?displayProperty=fullName>                        | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName>                     | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName>              | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions%601?displayProperty=fullName>              | Transient (vorübergehend) |
| <xref:Microsoft.Extensions.Options.IOptions%601?displayProperty=fullName>                       | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName>                             | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName>                           | Singleton |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* [NDC-Konferenzmuster für die DI-App-Entwicklung](https://www.youtube.com/watch?v=x-C-CNBVTaY)
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)
* [Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), und [Brandon Dahler](https://github.com/brandondahler)

ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.

Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Übersicht über Abhängigkeitsinjektion

Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt. Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:

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

Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen. Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:

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

Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab. Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:

* Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.
* Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden. In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.
* Diese Implementierung ist nicht für Komponententests geeignet. Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.

Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:

* Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.
* Registrierung der Abhängigkeit in einem Dienstcontainer. ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit. Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.
* Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird. Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.

In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an. Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet. Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an. Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück. Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.

`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden. `IMyDependency` ist in `Startup.ConfigureServices` registriert. `ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.

Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert. Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein. Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise). So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu. Es wird empfohlen, dass Apps dieser Konvention folgen. Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.

Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:

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

Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen. Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.

In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a>In den Start eingefügte Dienste

Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Dienste können in `Startup.Configure` eingefügt werden:

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="framework-provided-services"></a>Von Frameworks bereitgestellte Dienste

Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC. Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde. Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert. In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.

| Diensttyp | Lebensdauer |
| ------------ | -------- |
| <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient (vorübergehend) |
| <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient (vorübergehend) |
| <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton |
| <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient (vorübergehend) |
| <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton |
| <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient (vorübergehend) |
| <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton |
| <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |

## <a name="register-additional-services-with-extension-methods"></a>Registrieren weiterer Dienste mit Erweiterungsmethoden

Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren. Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> zusätzliche Dienste zum Container hinzugefügt werden:

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

Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.

## <a name="service-lifetimes"></a>Dienstlebensdauer

Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus. ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:

### <a name="transient"></a>Transient (vorübergehend)

Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt. Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.

In Apps, die Anforderungen verarbeiten, werden vorübergehende Dienste am Ende der Anforderung verworfen.

### <a name="scoped"></a>Bereichsbezogen

Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.

In Apps, die Anforderungen verarbeiten, werden bereichsbezogene Dienste am Ende der Anforderung verworfen.

> [!WARNING]
> Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen. Fügen Sie ihn nicht über [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt. Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.

### <a name="singleton"></a>Singleton

Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird). Jeder nachfolgenden Anforderung verwendet die gleiche Instanz. Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen. Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.

In Apps, die Anforderungen verarbeiten, werden Singleton-Dienste verworfen, wenn der <xref:Microsoft.Extensions.DependencyInjection.ServiceProvider> beim Herunterfahren der App verworfen wird.

> [!WARNING]
> Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen. Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.

## <a name="service-registration-methods"></a>Dienstregistrierungsmethoden

Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.

| Methode | Automatische<br>Objekt<br>bereinigung | Mehrere<br>Implementierungen | Argumentübergabe |
| ------ | :-----------------------------: | :-------------------------: | :-------: |
| `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`<br>Beispiel:<br>`services.AddSingleton<IMyDep, MyDep>();` | Ja | Ja | Nein |
| `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`<br>Beispiele:<br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br>`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Ja | Ja | Ja |
| `Add{LIFETIME}<{IMPLEMENTATION}>()`<br>Beispiel:<br>`services.AddSingleton<MyDep>();` | Ja | Nein | Nein |
| `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`<br>Beispiele:<br>`services.AddSingleton<IMyDep>(new MyDep());`<br>`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Nein | Ja | Ja |
| `AddSingleton(new {IMPLEMENTATION})`<br>Beispiele:<br>`services.AddSingleton(new MyDep());`<br>`services.AddSingleton(new MyDep("A string!"));` | Nein | Nein | Ja |

Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services). Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).

`TryAdd{LIFETIME}`-Methoden registrieren den Dienst nur, wenn noch keine Implementierung registriert ist.

Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`. Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

Weitere Informationen finden Sie unter:

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt. Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst. Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde. Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.

Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`. Die zweite Zeile registriert `MyDep` für `IMyDep2`. Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a>Verhalten von Constructor Injection

Dienste können durch zwei Mechanismen aufgelöst werden:

* <xref:System.IServiceProvider>
* <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Lässt die Erstellung von Objekten ohne Dienstregistrierung im Dependency-Injection-Container zu `ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.

Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.

Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.

Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist. Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.

## <a name="entity-framework-contexts"></a>Entity Framework-Kontexte

Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden. Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird. Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.

## <a name="lifetime-and-registration-options"></a>Lebensdauer und Registrierungsoptionen

Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen. Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Die Schnittstellen sind in die Klasse `Operation` implementiert. Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab. Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.

* Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`. `OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`. Der `OperationId`-Wert der neuen Instanz ist anders.
* Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung. Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.
* Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`. Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).

Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an. Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an. Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:

**Erste Anforderung:**

Controllervorgänge:

Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518  
Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instanz: 00000000-0000-0000-0000-000000000000

`OperationService`-Vorgänge:

Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instanz: 00000000-0000-0000-0000-000000000000

**Zweite Anforderung:**

Controllervorgänge:

Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instanz: 00000000-0000-0000-0000-000000000000

`OperationService`-Vorgänge:

Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instanz: 00000000-0000-0000-0000-000000000000

Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:

* Objekte vom Typ *Vorübergehend* sind immer unterschiedlich. Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich. Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.
* *Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.
* Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).

## <a name="call-services-from-main"></a>Abrufen von Diensten aus „Main“

Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen. Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen. Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:

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

## <a name="scope-validation"></a>Bereichsvalidierung

Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:

* Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden
* Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden

Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird. Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.

Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat. Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird. Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.

Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.   

## <a name="request-services"></a>Anfordern von Diensten

Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.

Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden. Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).

Generell sollte die App diese Eigenschaften nicht direkt verwenden. Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen. So erhalten Sie Klassen, die einfacher getestet werden können.

> [!NOTE]
> Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.

## <a name="design-services-for-dependency-injection"></a>Entwerfen von Diensten für die Abhängigkeitsinjektion

Best Practices:

* Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.
* Vermeiden Sie zustandsbehaftete statische Klassen und Member. Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.
* Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten. Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.
* Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.

Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt. Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben. Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt. Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).

### <a name="disposal-of-services"></a>Löschen von Diensten

Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf. Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.

Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:

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

Im folgenden Beispiel:

* werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.
* kennt das Framework die geplante Lebensdauer der Dienste nicht.
* verwirft das Framework die Dienste nicht automatisch.
* werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

### <a name="idisposable-guidance-for-transient-and-shared-instances"></a>IDisposable-Anleitung für vorübergehende and freigegebene Instanzen

#### <a name="transient-limited-lifetime"></a>Vorübergehende, eingeschränkte Lebensdauer

**Szenario**

Für die App ist eine <xref:System.IDisposable>-Instanz mit einer vorübergehenden Lebensdauer für eines der folgenden Szenarios erforderlich:

* Die-Instanz wird im Stammbereich aufgelöst.
* Die Instanz sollte verworfen werden, bevor der Bereich endet.

**Lösung**

Verwenden Sie das Factorymuster, um eine Instanz außerhalb des übergeordneten Bereichs zu erstellen. In dieser Situation verfügt die App in der Regel über eine `Create`-Methode, die den Konstruktor des endgültigen Typs direkt aufruft. Wenn der endgültige Typ andere Abhängigkeiten aufweist, kann die Factory folgende Aktionen ausführen:

* Sie kann <xref:System.IServiceProvider> im Konstruktor empfangen.
* Sie kann <xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities.CreateInstance%2A?displayProperty=nameWithType> verwenden, um eine Instanz außerhalb des Containers zu instanziieren und dabei den Container für die Abhängigkeiten verwenden.

#### <a name="shared-instance-limited-lifetime"></a>Freigegebene Instanz, eingeschränkte Lebensdauer

**Szenario**

Für die App ist eine freigegebene <xref:System.IDisposable>-Instanz über mehrere Dienste erforderlich, die <xref:System.IDisposable> sollte jedoch eine begrenzte Lebensdauer haben.

**Lösung**

Registrieren Sie die Instanz mit einer bereichsbezogenen Lebensdauer. Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope%2A?displayProperty=nameWithType> für den Start und zum Erstellen einer neuen <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>. Verwenden Sie die <xref:System.IServiceProvider>-Schnittstelle des Bereichs, um die erforderlichen Dienste abzurufen. Löschen Sie den Bereich, wenn die Lebensdauer beendet werden soll.

#### <a name="general-guidelines"></a>Allgemeine Richtlinien

* Registrieren Sie keine <xref:System.IDisposable>-Instanzen mit einem vorübergehenden Gültigkeitsbereich. Verwenden Sie stattdessen das Factorymuster.
* Lösen Sie keine vorübergehenden oder bereichsbezogenen <xref:System.IDisposable>-Instanzen im Stammbereich auf. Die einzige allgemeine Ausnahme gilt, wenn die App die <xref:System.IServiceProvider>-Instanz erstellt bzw. erneut erstellt und freigibt, was kein ideales Muster ist.
* Wenn eine <xref:System.IDisposable>-Abhängigkeit über DI empfangen wird, ist es nicht erforderlich, dass der Empfänger <xref:System.IDisposable> selbst implementiert. Der Empfänger der <xref:System.IDisposable>-Abhängigkeit darf <xref:System.IDisposable.Dispose%2A> auf dieser Abhängigkeit nicht abrufen.
* Bereiche sollten verwendet werden, um die Lebensdauer von Diensten zu steuern. Bereiche sind nicht hierarchisch, und es gibt keine besondere Verbindung zwischen den Bereichen.

## <a name="default-service-container-replacement"></a>Ersetzen von Standarddienstcontainern

Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen. Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:

* Eigenschaftsinjektion
* Auf Namen basierende Injektion
* Untergeordnete Container
* Benutzerdefinierte Verwaltung der Lebensdauer
* `Func<T>`-Unterstützung für die verzögerte Initialisierung
* Konventionsbasierte Registrierung

Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:

* [Autofac](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [DryIoc](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [Grace](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [LightInject](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [Lamar](https://jasperfx.github.io/lamar/)
* [Stashbox](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [Unity](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a>Threadsicherheit

Erstellen Sie threadsichere Singleton-Dienste. Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.

Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein. Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.

## <a name="recommendations"></a>Empfehlungen

* `async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt. C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.

* Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer. Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden. Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden. Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen. Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.
* Vermeiden Sie statischen Zugriff auf Dienste. Vermeiden Sie statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle.

* Vermeiden Sie die Verwendung des *Dienstlocatormusters*, da dieses [Steuerungsumkehrungsstrategien](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) vermischt.
  * Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Abhängigkeitsinjektion verwenden können:

    **Falsch:**

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
   
    **Richtig**:

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

* Vermeiden Sie die Injektion von Factorys, die Abhängigkeiten zur Laufzeit mit <xref:System.IServiceProvider.GetService*> auflösen.
* Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).

Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss. Es gibt nur wenige Ausnahmen, die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.

Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar. Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/fundamentals/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Vier Möglichkeiten zum Verwerfen von IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/)
* [Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)](/archive/msdn-magazine/2016/may/asp-net-writing-clean-code-in-asp-net-core-with-dependency-injection)
* [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)
* [Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
