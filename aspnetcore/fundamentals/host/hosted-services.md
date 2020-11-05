---
title: Hintergrundtasks mit gehosteten Diensten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Hintergrundtasks mit gehosteten Diensten in ASP.NET Core implementieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
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
uid: fundamentals/host/hosted-services
ms.openlocfilehash: b8d6ec079ed39fb3a2c314816ebae6cea0847a36
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061079"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a>Hintergrundtasks mit gehosteten Diensten in ASP.NET Core

Von [Jeow Li Huan](https://github.com/huan086)

::: moniker range=">= aspnetcore-3.0"

In ASP.NET Core können Hintergrundtasks als *gehostete Dienste* implementiert werden. Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert. In diesem Artikel sind drei Beispiel für gehostete Dienste enthalten:

* Hintergrundtasks, die auf einem Timer ausgeführt werden.
* Gehostete Dienste, die einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes) aktivieren. Der bereichsbezogene Dienst kann eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) verwenden.
* Hintergrundtasks in der Warteschlange, die sequenziell ausgeführt werden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="worker-service-template"></a>Vorlage „Workerdienst“

Die ASP.NET Core-Vorlage „Workerdienst“ dient als Ausgangspunkt für das Schreiben von Dienstanwendungen mit langer Laufzeit. Eine aus der Workerdienstvorlage erstellte App gibt das Worker SDK in ihrer Projektdatei an:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

Gehen Sie folgendermaßen vor, wenn Sie die Vorlage als Grundlage für eine Hosted Services-App verwenden möchten:

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a>Package

Eine App, die auf der Workerdienstvorlage basiert, verwendet das `Microsoft.NET.Sdk.Worker` SDK und verfügt über einen expliziten Paketverweis auf das [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)-Paket. Sehen Sie sich dazu beispielsweise die Projektdatei der Beispiel-App ( *BackgroundTasksSample.csproj* ) an.

Für Web-Apps, die das `Microsoft.NET.Sdk.Web` SDK verwenden, wird auf das [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)-Paket implizit über das geteilte Framework verwiesen. Es ist kein expliziter Paketverweis in der Projektdatei der App erforderlich.

## <a name="ihostedservice-interface"></a>Die IHostedService-Schnittstelle

Die <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle definiert zwei Methoden für Objekte, die vom Host verwaltet werden:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` enthält die Logik zum Starten des Hintergrundtasks. `StartAsync` wird *vor* folgenden Vorgängen aufgerufen:

  * Die App-Pipeline für die Anforderungsverarbeitung wird konfiguriert (`Startup.Configure`).
  * Der Server wird gestartet, und [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) wird ausgelöst.

  Das Standardverhalten kann so geändert werden, dass der `StartAsync`-Vorgang des gehosteten Diensts ausgeführt wird, nachdem die Pipeline der App konfiguriert und `ApplicationStarted` aufgerufen wurde. Um das Standardverhalten zu ändern, fügen Sie den gehosteten Dienst (`VideosWatcher` im folgenden Beispiel) nach dem Aufruf von `ConfigureWebHostDefaults` hinzu:

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Wird ausgelöst, wenn der Host ein ordnungsgemäßes Herunterfahren ausführt. `StopAsync` enthält die Logik zum Beenden des Hintergrundtasks. Implementieren Sie <xref:System.IDisposable> und [Finalizer (Destruktoren)](/dotnet/csharp/programming-guide/classes-and-structs/destructors), um nicht verwaltete Ressourcen zu löschen.

  Das Abbruchtoken hat standardmäßig ein Zeitlimit von fünf Sekunden, um zu melden, dass der Prozess des Herunterfahrens nicht mehr ordnungsgemäß ausgeführt wird. Gehen Sie wie folgt vor, wenn ein Abbruch für das Token angefordert wird:

  * Brechen Sie jegliche von der App ausgeführten Hintergrundoperationen ab.
  * Jegliche Methoden, die in `StopAsync` aufgerufen werden, sollten umgehend zurückgegeben werden.

  Allerdings werden keine Tasks abgebrochen, wenn der Abbruch angefordert wird. Der Aufrufer wartet, bis alle Tasks abgeschlossen sind.

  Wenn die App unerwartet beendet wird (weil der Prozess der App beispielsweise fehlschlägt), wird `StopAsync` möglicherweise nicht aufgerufen. Daher werden die in `StopAsync` aufgerufenen Methoden oder ausgeführten Operationen nicht durchgeführt.

  Um das standardmäßig 5-sekündige Timeout beim Herunterfahren zu verlängern, legen Sie folgendes fest:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>, wenn Sie den generischen Host verwenden. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Hostkonfigurationseinstellung „Timeout beim Herunterfahren“, wenn Sie den Webhost verwenden. Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#shutdown-timeout>.

Der gehostete Dienst wird beim Start der App einmal aktiviert und beim Beenden der App wieder ordnungsgemäß heruntergefahren. Wenn während der Ausführung von Hintergrundtasks ein Fehler ausgelöst wird, sollte `Dispose` aufgerufen werden, auch wenn `StopAsync` nicht aufgerufen wird.

## <a name="backgroundservice-base-class"></a>BackgroundService-Basisklasse

<xref:Microsoft.Extensions.Hosting.BackgroundService> ist eine Basisklasse zur Implementierung eines <xref:Microsoft.Extensions.Hosting.IHostedService> mit langer Laufzeit.

[ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) wird aufgerufen, um den Hintergrunddienst auszuführen. Die Implementierung gibt einen <xref:System.Threading.Tasks.Task> zurück, der die gesamte Lebensdauer des Hintergrunddiensts darstellt. Es werden keine weiteren Dienste gestartet, bis [ExecuteAsync asynchron wird](https://github.com/dotnet/extensions/issues/2149), etwa durch den Aufruf von `await`. Vermeiden Sie die Ausführung von langen, blockierenden Initialisierungsarbeiten in `ExecuteAsync`. Die Hostblöcke in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) warten auf den Abschluss von `ExecuteAsync`.

Das Abbruchtoken wird beim Aufruf von [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) ausgelöst. Ihre Implementierung von `ExecuteAsync` sollte unverzüglich beendet werden, wenn das Abbruchtoken ausgelöst wird, um den Dienst ordnungsgemäß herunterzufahren. Andernfalls wird der Dienst beim Erreichen des Timeouts beim Herunterfahren nicht ordnungsgemäß beendet. Weitere Informationen finden Sie im Abschnitt [IHostedService-Schnittstelle](#ihostedservice-interface).

## <a name="timed-background-tasks"></a>Zeitlich festgelegte Hintergrundtasks

Zeitlich festgelegte Hintergrundtasks verwenden die Klasse [System.Threading.Timer](xref:System.Threading.Timer). Der Timer löst die `DoWork`-Methode des Tasks aus. Der Timer wird durch `StopAsync` deaktiviert und freigegeben, wenn der Dienstcontainer durch `Dispose` freigegeben ist:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<xref:System.Threading.Timer> wartet nicht auf den Abschluss vorheriger Ausführungen von `DoWork`. Die veranschaulichte Vorgehensweise eignet sich also möglicherweise nicht für alle Szenarios. [Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) wird zum Erhöhen des Ausführungszählers mit einem atomischen Vorgang verwendet, wodurch sichergestellt wird, dass `executionCount` nicht durch mehrere Threads gleichzeitig aktualisiert wird.

Der Dienst wird in `IHostBuilder.ConfigureServices` ( *Program.cs* ) mit der Erweiterungsmethode `AddHostedService` registriert:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Verwenden eines bereichsbezogenen Diensts in einem Hintergrundtask

Erstellen Sie einen Bereich, um [bereichsbezogene Dienste](xref:fundamentals/dependency-injection#service-lifetimes) in einem [BackgroundService](#backgroundservice-base-class) zu verwenden. Bereiche werden für einen gehosteten Dienst nicht standardmäßig erstellt.

Der bereichsbezogene Dienst für Hintergrundtasks enthält die Logik des Hintergrundtasks. Im folgenden Beispiel:

* Der Dienst ist asynchron. Die `DoWork`-Methode gibt `Task` zurück. Zu Demonstrationszwecken wird in der `DoWork`-Methode eine Verzögerung von zehn Sekunden verwendet.
* Ein <xref:Microsoft.Extensions.Logging.ILogger> wird in den Dienst eingefügt.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Der gehostete Dienst erstellt einen Bereich, um den bereichsbezogenen Dienst für Hintergrundtasks aufzulösen, damit die `DoWork`-Methode aufgerufen wird. `DoWork` gibt einen `Task` zurück, auf den in `ExecuteAsync` gewartet wird:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

Die Dienste werden in `IHostBuilder.ConfigureServices` ( *Program.cs* ) registriert. Der gehostete Dienst wird mit der Erweiterungsmethode `AddHostedService` registriert:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Hintergrundtasks in der Warteschlange

Eine Warteschlange für Hintergrundaufgaben basiert auf dem .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

Im folgenden Beispiel für `QueueHostedService` gilt:

* Die `BackgroundProcessing`-Methode gibt einen `Task` zurück, auf den in `ExecuteAsync` gewartet wird.
* Hintergrundtasks in der Warteschlange werden aus dieser entfernt und in `BackgroundProcessing` ausgeführt.
* Auf Arbeitselemente wird gewartet, bevor der Dienst in `StopAsync` angehalten wird.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

Ein `MonitorLoop`-Dienst verarbeitet das Einreihen von Tasks in die Warteschlange für den gehosteten Dienst, wenn der `w`-Schlüssel auf einem Eingabegerät ausgewählt wird:

* Die `IBackgroundTaskQueue` wird in den `MonitorLoop`-Dienst eingefügt.
* `IBackgroundTaskQueue.QueueBackgroundWorkItem` wird aufgerufen, um ein Arbeitselement in die Warteschlange einzureihen.
* Das Arbeitselement simuliert eine Hintergrundaufgabe mit langer Ausführungszeit:
  * Drei 5-Sekunden-Verzögerungen werden ausgeführt (`Task.Delay`).
  * Eine `try-catch`-Anweisung fängt <xref:System.OperationCanceledException> auf, wenn der Task abgebrochen wird.

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

Die Dienste werden in `IHostBuilder.ConfigureServices` ( *Program.cs* ) registriert. Der gehostete Dienst wird mit der Erweiterungsmethode `AddHostedService` registriert:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

`MonitorLoop` wird in `Program.Main` gestartet:

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In ASP.NET Core können Hintergrundtasks als *gehostete Dienste* implementiert werden. Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert. In diesem Artikel sind drei Beispiel für gehostete Dienste enthalten:

* Hintergrundtasks, die auf einem Timer ausgeführt werden.
* Gehostete Dienste, die einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes) aktivieren. Der bereichsbezogene Dienst kann eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) verwenden.
* Hintergrundtasks in der Warteschlange, die sequenziell ausgeführt werden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="package"></a>Package

Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) hinzu.

## <a name="ihostedservice-interface"></a>Die IHostedService-Schnittstelle

Gehostete Dienste implementieren die <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle. Die Schnittstelle definiert zwei Methoden für Objekte, die vom Host verwaltet werden:

* [StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` enthält die Logik zum Starten des Hintergrundtasks. Bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) wird `StartAsync` aufgerufen, nachdem der Server gestartet und [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) ausgelöst wurde. Bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) wird `StartAsync` aufgerufen, bevor `ApplicationStarted` ausgelöst wird.

* [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Wird ausgelöst, wenn der Host ein ordnungsgemäßes Herunterfahren ausführt. `StopAsync` enthält die Logik zum Beenden des Hintergrundtasks. Implementieren Sie <xref:System.IDisposable> und [Finalizer (Destruktoren)](/dotnet/csharp/programming-guide/classes-and-structs/destructors), um nicht verwaltete Ressourcen zu löschen.

  Das Abbruchtoken hat standardmäßig ein Zeitlimit von fünf Sekunden, um zu melden, dass der Prozess des Herunterfahrens nicht mehr ordnungsgemäß ausgeführt wird. Gehen Sie wie folgt vor, wenn ein Abbruch für das Token angefordert wird:

  * Brechen Sie jegliche von der App ausgeführten Hintergrundoperationen ab.
  * Jegliche Methoden, die in `StopAsync` aufgerufen werden, sollten umgehend zurückgegeben werden.

  Allerdings werden keine Tasks abgebrochen, wenn der Abbruch angefordert wird. Der Aufrufer wartet, bis alle Tasks abgeschlossen sind.

  Wenn die App unerwartet beendet wird (weil der Prozess der App beispielsweise fehlschlägt), wird `StopAsync` möglicherweise nicht aufgerufen. Daher werden die in `StopAsync` aufgerufenen Methoden oder ausgeführten Operationen nicht durchgeführt.

  Um das standardmäßig 5-sekündige Timeout beim Herunterfahren zu verlängern, legen Sie folgendes fest:

  * <xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>, wenn Sie den generischen Host verwenden. Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#shutdown-timeout>.
  * Hostkonfigurationseinstellung „Timeout beim Herunterfahren“, wenn Sie den Webhost verwenden. Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#shutdown-timeout>.

Der gehostete Dienst wird beim Start der App einmal aktiviert und beim Beenden der App wieder ordnungsgemäß heruntergefahren. Wenn während der Ausführung von Hintergrundtasks ein Fehler ausgelöst wird, sollte `Dispose` aufgerufen werden, auch wenn `StopAsync` nicht aufgerufen wird.

## <a name="timed-background-tasks"></a>Zeitlich festgelegte Hintergrundtasks

Zeitlich festgelegte Hintergrundtasks verwenden die Klasse [System.Threading.Timer](xref:System.Threading.Timer). Der Timer löst die `DoWork`-Methode des Tasks aus. Der Timer wird durch `StopAsync` deaktiviert und freigegeben, wenn der Dienstcontainer durch `Dispose` freigegeben ist:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<xref:System.Threading.Timer> wartet nicht auf den Abschluss vorheriger Ausführungen von `DoWork`. Die veranschaulichte Vorgehensweise eignet sich also möglicherweise nicht für alle Szenarios.

Der Dienst wird in `Startup.ConfigureServices` mit der Erweiterungsmethode `AddHostedService` registriert:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a>Verwenden eines bereichsbezogenen Diensts in einem Hintergrundtask

Erstellen Sie einen Bereich, um [bereichsbezogene Dienste](xref:fundamentals/dependency-injection#service-lifetimes) in `IHostedService` zu verwenden. Bereiche werden für einen gehosteten Dienst nicht standardmäßig erstellt.

Der bereichsbezogene Dienst für Hintergrundtasks enthält die Logik des Hintergrundtasks. Im folgenden Beispiel wird <xref:Microsoft.Extensions.Logging.ILogger> in den Dienst eingefügt:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

Der gehostete Dienst erstellt einen Bereich, um den bereichsbezogenen Dienst für Hintergrundtasks aufzulösen, um die `DoWork`-Methode aufzurufen:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

Die Dienste werden in `Startup.ConfigureServices` registriert. Der Implementierung `IHostedService` wird mit der Erweiterungsmethode `AddHostedService` registriert:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a>Hintergrundtasks in der Warteschlange

Eine Warteschlange für Hintergrundtasks basiert auf dem <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> von .NET Framework 4.x ([die Integration in ASP.NET Core ist vorläufig geplant](https://github.com/aspnet/Hosting/issues/1280)):

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

In `QueueHostedService` werden Hintergrundaufgaben in der Warteschlange aus der Warteschlange entfernt und als [BackgroundService](#backgroundservice-base-class), eine Basisklasse zum Bereitstellen von `IHostedService` mit langer Ausführungszeit, ausgeführt:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

Die Dienste werden in `Startup.ConfigureServices` registriert. Der Implementierung `IHostedService` wird mit der Erweiterungsmethode `AddHostedService` registriert:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

In der Modellklasse für die Indexseite:

* `IBackgroundTaskQueue` wird in den Konstruktor eingefügt und `Queue` zugewiesen.
* Ein <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> wird eingefügt und `_serviceScopeFactory` zugewiesen. Die Zuordnungsinstanz wird verwendet, um Instanzen von <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> zu erstellen. Dieser wird verwendet, um Dienste in einem Bereich zu erstellen. Ein Bereich wird erstellt, um den `AppDbContext` der App (einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes)) zu verwenden, um Datenbankeinträge in `IBackgroundTaskQueue` (ein Singletondienst) zu schreiben.

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

Wenn auf der Indexseite auf die Schaltfläche **Task hinzufügen** geklickt wird, wird die `OnPostAddTask`-Methode ausgeführt. `QueueBackgroundWorkItem` wird aufgerufen, um ein Arbeitselement in die Warteschlange einzureihen:

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Implement background tasks in microservices with IHostedService and the BackgroundService class (Implementieren von Hintergrundtasks in Microservices mit IHostedService und der BackgroundService-Klasse)](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* [Ausführen von Hintergrundaufgaben mit WebJobs in Azure App Service](/azure/app-service/webjobs-create)
* <xref:System.Threading.Timer>
