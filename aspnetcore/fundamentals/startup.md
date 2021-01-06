---
title: Anwendungsstart in ASP.NET Core
author: rick-anderson
description: Informationen zur Funktionsweise der Startklasse in ASP.NET Core bei der Konfiguration von Diensten und der Anforderungspipeline einer App.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/startup
ms.openlocfilehash: 747b13abb0ce3fed2d1dc018c6dbf82db1ae7130
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93052239"
---
# <a name="app-startup-in-aspnet-core"></a>Anwendungsstart in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) und [Steve Smith](https://ardalis.com)

Die `Startup`-Klasse konfiguriert Dienste und die Anforderungspipeline einer App.

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a>Die Startup-Klasse

ASP.NET Core-Apps verwenden eine `Startup`-Klasse, die standardmäßig `Startup` genannt wird. Die `Startup`-Klasse:

* Kann eine <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>-Methode enthalten, um die *Dienste* der App zu konfigurieren. Ein Dienst ist eine wiederverwendbare Komponente, die App-Funktionalität bereitstellt. Dienste werden in `ConfigureServices`*registriert* und in der App über [Dependency Injection (DI)](xref:fundamentals/dependency-injection) oder <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> genutzt.
* Enthält eine <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>-Methode, um die Anforderungsverarbeitungspipeline einer App zu erstellen.

`ConfigureServices` und `Configure` werden von der ASP.NET Core-Runtime aufgerufen, wenn die App gestartet wird:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Das Beispiel oben bezieht sich auf [Razor Pages](xref:razor-pages/index). Die MVC-Version ist aber ähnlich.


Die `Startup`-Klasse wird angegeben, wenn der [Host](xref:fundamentals/index#host) der App erstellt wird. Die `Startup`-Klasse wird in der Regel durch Aufruf der [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*)-Methode im Hostgenerator angegeben:

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

Der Host stellt Dienste für den `Startup`-Klassenkonstruktor bereit. Die App fügt über `ConfigureServices` zusätzliche Dienste hinzu. Sowohl die Host- als auch die App-Dienste sind in `Configure` und über die App verfügbar.

Bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

Die meisten Dienste sind erst verfügbar, wenn die `Configure`-Methode aufgerufen wird.

### <a name="multiple-startup"></a>Mehrere Startup-Klassen

Wenn die App unterschiedliche `Startup`-Klassen für unterschiedliche Umgebungen definiert (z.B. `StartupDevelopment`), wird zur Laufzeit die entsprechende `Startup`-Klasse ausgewählt. Die Klasse, deren Namenssuffix mit der aktuellen Umgebung übereinstimmt, wird priorisiert. Wenn die App in der Entwicklungsumgebung ausgeführt wird und sowohl eine `Startup`-Klasse als auch eine `StartupDevelopment`-Klasse enthält, wird die `StartupDevelopment`-Klasse verwendet. Weitere Informationen finden Sie unter [Verwenden mehrerer Umgebungen](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Weitere Informationen zum Host finden Sie unter [Der Host](xref:fundamentals/index#host). Weitere Informationen zum Umgang mit Fehlern beim Start finden Sie unter [Startup exception handling (Umgang mit Ausnahmen beim Start)](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Die ConfigureServices-Methode

Die <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>-Methode:

* Dies ist optional.
* wird vor der `Configure`-Methode vom Host aufgerufen, um die App-Dienste zu konfigurieren.
* Über diese Methode werden standardmäßig [Konfigurationsoptionen](xref:fundamentals/configuration/index) festgelegt.

Es kann sein, dass der Host einige Dienste konfiguriert, bevor die `Startup`-Methoden aufgerufen werden. Weitere Informationen finden Sie unter [Der Host](xref:fundamentals/index#host).

Für Features, die ein umfangreiches Setup erfordern, sind in <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>`Add{Service}`-Erweiterungsmethoden verfügbar. Beispiel: **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores und **Add**RazorPages:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

Wenn Sie Dienste zum Dienstcontainer hinzufügen, können Sie auch über die App und die `Configure`-Methode auf diese zugreifen. Die Dienste werden über die [Dependency Injection](xref:fundamentals/dependency-injection) oder über <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> aufgelöst.

## <a name="the-configure-method"></a>Die Configure-Methode

Die <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>-Methode wird verwendet, um festzulegen, wie die App auf HTTP-Anforderungen reagiert. Sie können die Anforderungspipeline konfigurieren, indem Sie [Middlewarekomponenten](xref:fundamentals/middleware/index) zu einer <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>-Instanz hinzufügen. Die `Configure`-Methode kann auf `IApplicationBuilder` zugreifen, wird aber nicht im Dienstcontainer registriert. Über das Hosting wird ein `IApplicationBuilder` erstellt, der direkt an `Configure` übergeben wird.

Die [ASP.NET Core-Vorlagen](/dotnet/core/tools/dotnet-new) konfigurieren die Pipeline mit Unterstützung für:

* [die Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling#developer-exception-page)
* [Ausnahmehandler](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [HTTPS-Umleitung](xref:security/enforcing-ssl)
* [Statische Dateien](xref:fundamentals/static-files)
* ASP.NET Core-[MVC](xref:mvc/overview) und [Razor Pages](xref:razor-pages/index)


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

Das Beispiel oben bezieht sich auf [Razor Pages](xref:razor-pages/index). Die MVC-Version ist aber ähnlich.

Jede `Use`-Erweiterungsmethode fügt mindestens eine Middlewarekomponente zu der Anforderungspipeline hinzu. Beispielsweise konfiguriert <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>[Middleware](xref:fundamentals/middleware/index) für die Bereitstellung [statischer Dateien](xref:fundamentals/static-files).

Jede Middlewarekomponente in der Anforderungspipeline ist für das Aufrufen der jeweils nächsten Komponente in der Pipeline oder, wenn nötig, für das Kurzschließen der Kette zuständig.

In der `Configure`-Methodensignatur können außerdem auch zusätzliche Dienste wie `IWebHostEnvironment`, `ILoggerFactory` oder in `ConfigureServices` definierte Dienste angegeben werden. Diese Dienste werden eingefügt, wenn Sie verfügbar sind.

Weitere Informationen zur Verwendung von `IApplicationBuilder` und der Reihenfolge der Middlewareverarbeitung finden Sie unter <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurieren von Diensten ohne Start

Rufen Sie die Hilfsmethoden `ConfigureServices` und `Configure` im Hostgenerator auf, um Dienste und die Anforderungsverarbeitungspipeline ohne die `Startup`-Klasse zu konfigurieren. Wenn `ConfigureServices` mehrmals aufgerufen wird, werden diese Aufrufe einander angefügt. Wenn es mehrere Aufrufe der Methode `Configure` gibt, wird der letzte `Configure`-Aufruf verwendet.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a>Erweitern Sie den Start mit Startfiltern

Verwenden Sie <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:

* Um Middleware am Anfang und Ende der [Configure](#the-configure-method)-Middlewarepipeline einer App zu konfigurieren, ohne `Use{Middleware}` explizit aufzurufen. `IStartupFilter` wird von ASP.NET Core verwendet, um Standardwerte am Anfang der Pipeline hinzuzufügen, ohne dass der App-Autor die Standardmiddleware explizit registrieren lassen muss. `IStartupFilter` ermöglicht im Auftrag des App-Autors einen Aufruf einer anderen Komponente `Use{Middleware}`.
* Um eine Pipeline aus `Configure`-Methoden zu erstellen. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) kann festlegen, dass eine Middleware vor oder nach dem Hinzufügen von Middleware durch Bibliotheken ausgeführt wird.

`IStartupFilter` implementiert <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>. Das Element empfängt einen `Action<IApplicationBuilder>` und gibt diesen zurück. Eine <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>-Schnittstelle definiert Klassen, um die Anforderungspipeline einer App zu konfigurieren. Weitere Informationen finden Sie unter [Erstellen einer Middlewarepipeline mit IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Jeder `IStartupFilter` kann mindestens eine Middlewarekomponente in der Anforderungspipeline hinzufügen. Die Filter werden in der Reihenfolge aufgerufen, in der sie zum Dienstcontainer hinzugefügt wurden. Über Filter kann Middleware hinzugefügt werden, bevor oder nachdem dem nächsten Filter die Kontrolle erteilt wird. D.h., sie wird am Anfang oder am Ende einer App-Pipeline hinzugefügt.

Im folgenden Beispiel wird veranschaulicht, wie Middleware bei `IStartupFilter` registriert wird. Die `RequestSetOptionsMiddleware`-Middleware legt einen Optionswert über einen Parameter der Abfragezeichenfolge fest:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

Die `RequestSetOptionsMiddleware`-Klasse wird in der `RequestSetOptionsStartupFilter`-Klasse konfiguriert:

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

Der `IStartupFilter` wird im Dienstcontainer in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> registriert.

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

Wenn für `option` ein Parameter der Abfragezeichenfolge bereitgestellt wird, verarbeitet die Middleware die Wertzuweisung, bevor die ASP.NET Core-Middleware die Antwort rendert.

Die Reihenfolge der Ausführung der Middleware ist von der Reihenfolge der `IStartupFilter`-Registrierungen abhängig:

* Es kann sein, dass mehrere Implementierungen von `IStartupFilter` mit denselben Objekten interagieren. Wenn die Reihenfolge für Sie wichtig ist, sollten Sie die `IStartupFilter`-Dienstregistrierungen der Implementierungen in der Reihenfolge anordnen, in der die Middleware ausgeführt werden soll.
* Bibliotheken fügen möglicherweise Middleware mit mindestens einer `IStartupFilter`-Implementierung hinzu, die vor oder nach anderer App-Middleware ausgeführt wird, die über `IStartupFilter` registriert wurde. So rufen Sie `IStartupFilter`-Middleware auf, bevor eine Middleware durch den `IStartupFilter` von einer Bibliothek hinzugefügt wird:

  * Positionieren Sie die Dienstregistrierung, bevor die Bibliothek dem Dienstcontainer hinzugefügt wird.
  * Wenn sie danach aufgerufen werden soll, positionieren Sie die Dienstregistrierung, nachdem die Bibliothek hinzugefügt wurde.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Hinzufügen von Konfigurationen aus einer externen Assembly beim Start

Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Der Host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a>Die Startup-Klasse

ASP.NET Core-Apps verwenden eine `Startup`-Klasse, die standardmäßig `Startup` genannt wird. Die `Startup`-Klasse:

* Kann eine <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>-Methode enthalten, um die *Dienste* der App zu konfigurieren. Ein Dienst ist eine wiederverwendbare Komponente, die App-Funktionalität bereitstellt. Dienste werden in `ConfigureServices`*registriert* und in der App über [Dependency Injection (DI)](xref:fundamentals/dependency-injection) oder <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> genutzt.
* Enthält eine <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>-Methode, um die Anforderungsverarbeitungspipeline einer App zu erstellen.

`ConfigureServices` und `Configure` werden von der ASP.NET Core-Runtime aufgerufen, wenn die App gestartet wird:

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

Die `Startup`-Klasse wird angegeben, wenn der [Host](xref:fundamentals/index#host) der App erstellt wird. Die `Startup`-Klasse wird in der Regel durch Aufruf der [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*)-Methode im Hostgenerator angegeben:

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

Der Host stellt Dienste für den `Startup`-Klassenkonstruktor bereit. Die App fügt über `ConfigureServices` zusätzliche Dienste hinzu. Daher sind sowohl die Host- als auch die App-Dienste in `Configure` und über die App verfügbar.

[Dependency Injection](xref:fundamentals/dependency-injection) wird häufig im Zusammenhang mit der `Startup`-Klasse verwendet, um Folgendes einzufügen:

* <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>, um Dienste nach Umgebung zu konfigurieren.
* <xref:Microsoft.Extensions.Configuration.IConfiguration>, um Konfigurationen zu lesen.
* <xref:Microsoft.Extensions.Logging.ILoggerFactory>, um eine Protokollierung in `Startup.ConfigureServices` zu erstellen.

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

Die meisten Dienste sind erst verfügbar, wenn die `Configure`-Methode aufgerufen wird.

### <a name="multiple-startup"></a>Mehrere Startup-Klassen

Wenn die App unterschiedliche `Startup`-Klassen für unterschiedliche Umgebungen definiert (z.B. `StartupDevelopment`), wird zur Laufzeit die entsprechende `Startup`-Klasse ausgewählt. Die Klasse, deren Namenssuffix mit der aktuellen Umgebung übereinstimmt, wird priorisiert. Wenn die App in der Entwicklungsumgebung ausgeführt wird und sowohl eine `Startup`-Klasse als auch eine `StartupDevelopment`-Klasse enthält, wird die `StartupDevelopment`-Klasse verwendet. Weitere Informationen finden Sie unter [Verwenden mehrerer Umgebungen](xref:fundamentals/environments#environment-based-startup-class-and-methods).

Weitere Informationen zum Host finden Sie unter [Der Host](xref:fundamentals/index#host). Weitere Informationen zum Umgang mit Fehlern beim Start finden Sie unter [Startup exception handling (Umgang mit Ausnahmen beim Start)](xref:fundamentals/error-handling#startup-exception-handling).

## <a name="the-configureservices-method"></a>Die ConfigureServices-Methode

Die <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>-Methode:

* Dies ist optional.
* wird vor der `Configure`-Methode vom Host aufgerufen, um die App-Dienste zu konfigurieren.
* Über diese Methode werden standardmäßig [Konfigurationsoptionen](xref:fundamentals/configuration/index) festgelegt.

Es kann sein, dass der Host einige Dienste konfiguriert, bevor die `Startup`-Methoden aufgerufen werden. Weitere Informationen finden Sie unter [Der Host](xref:fundamentals/index#host).

Für Features, die ein umfangreiches Setup erfordern, sind in <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>`Add{Service}`-Erweiterungsmethoden verfügbar. Beispiel: **Add** DbContext, **Add** DefaultIdentity, **Add** EntityFrameworkStores und **Add**RazorPages:

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

Wenn Sie Dienste zum Dienstcontainer hinzufügen, können Sie auch über die App und die `Configure`-Methode auf diese zugreifen. Die Dienste werden über die [Dependency Injection](xref:fundamentals/dependency-injection) oder über <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> aufgelöst.

Unter [SetCompatibilityVersion](xref:mvc/compatibility-version) finden Sie weitere Informationen zu `SetCompatibilityVersion`.

## <a name="the-configure-method"></a>Die Configure-Methode

Die <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>-Methode wird verwendet, um festzulegen, wie die App auf HTTP-Anforderungen reagiert. Sie können die Anforderungspipeline konfigurieren, indem Sie [Middlewarekomponenten](xref:fundamentals/middleware/index) zu einer <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>-Instanz hinzufügen. Die `Configure`-Methode kann auf `IApplicationBuilder` zugreifen, wird aber nicht im Dienstcontainer registriert. Über das Hosting wird ein `IApplicationBuilder` erstellt, der direkt an `Configure` übergeben wird.

Die [ASP.NET Core-Vorlagen](/dotnet/core/tools/dotnet-new) konfigurieren die Pipeline mit Unterstützung für:

* [die Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling#developer-exception-page)
* [Ausnahmehandler](xref:fundamentals/error-handling#exception-handler-page)
* [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [HTTPS-Umleitung](xref:security/enforcing-ssl)
* [Statische Dateien](xref:fundamentals/static-files)
* ASP.NET Core-[MVC](xref:mvc/overview) und [Razor Pages](xref:razor-pages/index)
* [die Datenschutzgrundverordnung (DSGVO)](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

Jede `Use`-Erweiterungsmethode fügt mindestens eine Middlewarekomponente zu der Anforderungspipeline hinzu. Beispielsweise konfiguriert <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>[Middleware](xref:fundamentals/middleware/index) für die Bereitstellung [statischer Dateien](xref:fundamentals/static-files).

Jede Middlewarekomponente in der Anforderungspipeline ist für das Aufrufen der jeweils nächsten Komponente in der Pipeline oder, wenn nötig, für das Kurzschließen der Kette zuständig.

In der `Configure`-Methodensignatur können außerdem auch zusätzliche Dienste wie `IHostingEnvironment` und `ILoggerFactory` oder in `ConfigureServices` definierte Dienste angegeben werden. Diese Dienste werden eingefügt, wenn Sie verfügbar sind.

Weitere Informationen zur Verwendung von `IApplicationBuilder` und der Reihenfolge der Middlewareverarbeitung finden Sie unter <xref:fundamentals/middleware/index>.

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a>Konfigurieren von Diensten ohne Start

Rufen Sie die Hilfsmethoden `ConfigureServices` und `Configure` im Hostgenerator auf, um Dienste und die Anforderungsverarbeitungspipeline ohne die `Startup`-Klasse zu konfigurieren. Wenn `ConfigureServices` mehrmals aufgerufen wird, werden diese Aufrufe einander angefügt. Wenn es mehrere Aufrufe der Methode `Configure` gibt, wird der letzte `Configure`-Aufruf verwendet.

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a>Erweitern Sie den Start mit Startfiltern

Verwenden Sie <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:

* Um Middleware am Anfang und Ende der [Configure](#the-configure-method)-Middlewarepipeline einer App zu konfigurieren, ohne `Use{Middleware}` explizit aufzurufen. `IStartupFilter` wird von ASP.NET Core verwendet, um Standardwerte am Anfang der Pipeline hinzuzufügen, ohne dass der App-Autor die Standardmiddleware explizit registrieren lassen muss. `IStartupFilter` ermöglicht im Auftrag des App-Autors einen Aufruf einer anderen Komponente `Use{Middleware}`.
* Um eine Pipeline aus `Configure`-Methoden zu erstellen. [IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) kann festlegen, dass eine Middleware vor oder nach dem Hinzufügen von Middleware durch Bibliotheken ausgeführt wird.

`IStartupFilter` implementiert <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>. Das Element empfängt einen `Action<IApplicationBuilder>` und gibt diesen zurück. Eine <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>-Schnittstelle definiert Klassen, um die Anforderungspipeline einer App zu konfigurieren. Weitere Informationen finden Sie unter [Erstellen einer Middlewarepipeline mit IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).

Jeder `IStartupFilter` kann mindestens eine Middlewarekomponente in der Anforderungspipeline hinzufügen. Die Filter werden in der Reihenfolge aufgerufen, in der sie zum Dienstcontainer hinzugefügt wurden. Über Filter kann Middleware hinzugefügt werden, bevor oder nachdem dem nächsten Filter die Kontrolle erteilt wird. D.h., sie wird am Anfang oder am Ende einer App-Pipeline hinzugefügt.

Im folgenden Beispiel wird veranschaulicht, wie Middleware bei `IStartupFilter` registriert wird. Die `RequestSetOptionsMiddleware`-Middleware legt einen Optionswert über einen Parameter der Abfragezeichenfolge fest:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

Die `RequestSetOptionsMiddleware`-Klasse wird in der `RequestSetOptionsStartupFilter`-Klasse konfiguriert:

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

Der `IStartupFilter` wird im Dienstcontainer in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> registriert.

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

Wenn für `option` ein Parameter der Abfragezeichenfolge bereitgestellt wird, verarbeitet die Middleware die Wertzuweisung, bevor die ASP.NET Core-Middleware die Antwort rendert.

Die Reihenfolge der Ausführung der Middleware ist von der Reihenfolge der `IStartupFilter`-Registrierungen abhängig:

* Es kann sein, dass mehrere Implementierungen von `IStartupFilter` mit denselben Objekten interagieren. Wenn die Reihenfolge für Sie wichtig ist, sollten Sie die `IStartupFilter`-Dienstregistrierungen der Implementierungen in der Reihenfolge anordnen, in der die Middleware ausgeführt werden soll.
* Bibliotheken fügen möglicherweise Middleware mit mindestens einer `IStartupFilter`-Implementierung hinzu, die vor oder nach anderer App-Middleware ausgeführt wird, die über `IStartupFilter` registriert wurde. So rufen Sie `IStartupFilter`-Middleware auf, bevor eine Middleware durch den `IStartupFilter` von einer Bibliothek hinzugefügt wird:

  * Positionieren Sie die Dienstregistrierung, bevor die Bibliothek dem Dienstcontainer hinzugefügt wird.
  * Wenn sie danach aufgerufen werden soll, positionieren Sie die Dienstregistrierung, nachdem die Bibliothek hinzugefügt wurde.

## <a name="add-configuration-at-startup-from-an-external-assembly"></a>Hinzufügen von Konfigurationen aus einer externen Assembly beim Start

Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Der Host](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end