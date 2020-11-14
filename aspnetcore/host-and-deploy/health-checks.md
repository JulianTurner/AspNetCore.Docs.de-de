---
title: Integritätsprüfungen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Integritätsprüfungen für ASP.NET Core-Infrastrukturelemente wie Apps und Datenbanken einrichten.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 06/22/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/health-checks
ms.openlocfilehash: 32b7a4c6722ba45ba998f9430f5d6da6ddca53f9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058661"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="9f31c-103">Integritätsprüfungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9f31c-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="9f31c-104">Von [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="9f31c-104">By [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9f31c-105">ASP.NET Core bietet Middleware für Integritätsprüfungen und Bibliotheken für die Berichterstellung für Komponenten der App-Infrastruktur.</span><span class="sxs-lookup"><span data-stu-id="9f31c-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="9f31c-106">Integritätsprüfungen werden von einer App als HTTP-Endpunkte verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="9f31c-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="9f31c-107">Integritätsprüfungs-Endpunkte können für eine Vielzahl von Echtzeit-Überwachungsszenarien konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="9f31c-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="9f31c-108">Integritätstests können von Containerorchestratoren und Lastenausgleichsmodulen verwendet werden, um den Status einer App zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="9f31c-109">Ein Containerorchestrator kann z.B. auf eine fehlerhafte Integritätsprüfung reagieren, indem die Ausführung einer Bereitstellung angehalten oder ein Container neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="9f31c-110">Ein Lastenausgleichsmodul kann auf eine fehlerhafte App reagieren, indem Datenverkehr von der fehlerhaften zu einer fehlerfreien Instanz umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="9f31c-111">Die Nutzung von Arbeitsspeicher, Datenträgern und anderen physischen Serverressourcen kann auf einen fehlerfreien Status überwacht werden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="9f31c-112">Integritätsprüfungen können die Abhängigkeiten einer App testen, wie z.B. Datenbanken und externe Dienstendpunkte, um Verfügbarkeit und normale Funktionsweise zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="9f31c-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f31c-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9f31c-114">Die Beispiel-App enthält Beispiele der Szenarien, die in diesem Thema beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="9f31c-115">Um die Beispiel-App für ein bestimmtes Szenario auszuführen, verwenden Sie den Befehl [dotnet run](/dotnet/core/tools/dotnet-run) aus dem Ordner des Projekts in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="9f31c-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="9f31c-116">Informationen zur Verwendung der Beispiel-App finden Sie in der Datei *README.md* der Beispiel-App und den Szenariobeschreibungen in diesem Thema.</span><span class="sxs-lookup"><span data-stu-id="9f31c-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9f31c-117">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="9f31c-117">Prerequisites</span></span>

<span data-ttu-id="9f31c-118">Integritätsprüfungen werden in der Regel mit einem externen Überwachungsdienst oder Containerorchestrator verwendet, um den Status einer App zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="9f31c-119">Bevor Sie Integritätsprüfungen zu einer App hinzufügen, entscheiden Sie, welches Überwachungssystem verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="9f31c-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="9f31c-120">Das Überwachungssystem bestimmt, welche Arten von Integritätsprüfungen erstellt und wie die jeweiligen Endpunkte konfiguriert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="9f31c-121">Für ASP.NET Core-Apps wird implizit auf das [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks)-Paket verwiesen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-121">The [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package is referenced implicitly for ASP.NET Core apps.</span></span> <span data-ttu-id="9f31c-122">Fügen Sie zum Durchführen von Integritätsprüfungen mit Entity Framework Core einen Paketverweis auf das [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="9f31c-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="9f31c-123">Die Beispiel-App stellt Startcode bereit, um die Integritätsprüfungen für verschiedene Szenarien zu veranschaulichen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="9f31c-124">Das Szenario [Datenbanktest](#database-probe) überprüft die Integrität einer Datenbankverbindung mithilfe von [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="9f31c-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="9f31c-125">Das Szenario [DbContext-Test](#entity-framework-core-dbcontext-probe) überprüft eine Datenbank mithilfe eines EF Core-`DbContext`-Elements.</span><span class="sxs-lookup"><span data-stu-id="9f31c-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="9f31c-126">Für ein Erkunden der Datenbankszenarios gilt für die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="9f31c-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="9f31c-127">Sie erstellt eine Datenbank und stellt deren Verbindungszeichenfolge in der *appsettings.json* -Datei bereit.</span><span class="sxs-lookup"><span data-stu-id="9f31c-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="9f31c-128">Sie hat die folgenden Paketverweise in ihrer Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="9f31c-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="9f31c-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="9f31c-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="9f31c-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9f31c-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="9f31c-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="9f31c-132">Ein anderes Szenario für Integritätsprüfungen zeigt, wie Sie Integritätsprüfungen auf einen Verwaltungsport filtern können.</span><span class="sxs-lookup"><span data-stu-id="9f31c-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="9f31c-133">Für die Beispiel-App müssen Sie eine *Properties/launchSettings.json* -Datei erstellen, die die Verwaltungs-URL und den Verwaltungsport enthält.</span><span class="sxs-lookup"><span data-stu-id="9f31c-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="9f31c-134">Weitere Informationen finden Sie im Abschnitt [Filtern nach Port](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="9f31c-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="9f31c-135">Grundlegender Integritätstest</span><span class="sxs-lookup"><span data-stu-id="9f31c-135">Basic health probe</span></span>

<span data-ttu-id="9f31c-136">In vielen Apps genügt zur Ermittlung des App-Status eine grundlegende Integritätstestkonfiguration, die die Verfügbarkeit der App für die Verarbeitung von Anforderungen ( *Lebendigkeit* ) meldet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-136">For many apps, a basic health probe configuration that reports the app's availability to process requests ( *liveness* ) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="9f31c-137">Die grundlegende Konfiguration registriert Integritätsprüfungsdienste und ruft die Middleware für Integritätsprüfungen auf, damit diese an einem URL-Endpunkt mit dem Integritätsstatus antwortet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="9f31c-138">Standardmäßig werden keine spezifischen Integritätsprüfungen registriert, um eine bestimmte Abhängigkeit oder ein bestimmtes Subsystem zu testen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="9f31c-139">Die App wird als fehlerfrei angesehen, wenn sie an der URL des Integritätsendpunkts antworten kann.</span><span class="sxs-lookup"><span data-stu-id="9f31c-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="9f31c-140">Der standardmäßige Antwortwriter schreibt den Status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) als Klartextantwort zurück an den Client und gibt den Status als [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) oder [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) an.</span><span class="sxs-lookup"><span data-stu-id="9f31c-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="9f31c-141">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f31c-142">Erstellen Sie einen Integritätsprüfungs-Endpunkt durch Aufrufen von `MapHealthChecks` in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="9f31c-143">In der Beispiel-App wird der Integritätsprüfungs-Endpunkt in `/health` ( *BasicStartup.cs* ) erstellt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-143">In the sample app, the health check endpoint is created at `/health` ( *BasicStartup.cs* ):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="9f31c-144">Um das Szenario für die grundlegende Konfiguration mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="9f31c-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="9f31c-145">Docker-Beispiel</span><span class="sxs-lookup"><span data-stu-id="9f31c-145">Docker example</span></span>

<span data-ttu-id="9f31c-146">[Docker](xref:host-and-deploy/docker/index) bietet eine integrierte `HEALTHCHECK`-Direktive, mit der Sie den Status einer App überprüfen können, die die grundlegende Konfiguration für Integritätsprüfungen verwendet:</span><span class="sxs-lookup"><span data-stu-id="9f31c-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="9f31c-147">Erstellen von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="9f31c-147">Create health checks</span></span>

<span data-ttu-id="9f31c-148">Integritätsprüfungen werden durch Implementieren der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>-Schnittstelle erstellt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="9f31c-149">Die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>-Methode gibt ein <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zurück, das die Integrität als `Healthy`, `Degraded` oder `Unhealthy` angibt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="9f31c-150">Das Ergebnis wird als Klartextantwort mit einem konfigurierbaren Statuscode geschrieben (diese Konfiguration wird im Abschnitt [Optionen für die Integritätsprüfung](#health-check-options) beschrieben).</span><span class="sxs-lookup"><span data-stu-id="9f31c-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="9f31c-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> kann auch optionale Schlüssel-Wert-Paare zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="9f31c-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="9f31c-152">Die folgende `ExampleHealthCheck`-Klasse veranschaulicht das Layout einer Integritätsprüfung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="9f31c-153">Die Integritätsprüfungslogik wird in der `CheckHealthAsync`-Methode platziert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="9f31c-154">Im folgenden Beispiel wird eine Dummyvariable `healthCheckResultHealthy` auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="9f31c-155">Wenn der Wert von `healthCheckResultHealthy` auf `false` festgelegt ist, wird der [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*)-Status zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="9f31c-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="9f31c-156">Registrieren von Integritätsprüfungsdiensten</span><span class="sxs-lookup"><span data-stu-id="9f31c-156">Register health check services</span></span>

<span data-ttu-id="9f31c-157">Der `ExampleHealthCheck`-Typ wird mit <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` den Integritätsprüfungsdiensten hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="9f31c-158">Die in der folgenden Abbildung gezeigte <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>-Überladung legt den Fehlerstatus (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) fest, der angegeben werden soll, wenn die Integritätsprüfung einen Fehler meldet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="9f31c-159">Wenn der Fehlerstatus auf `null` (Standardwert) festgelegt ist, wird [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) gemeldet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="9f31c-160">Diese Überladung ist ein nützliches Szenario für Bibliotheksersteller: Bei einem Integritätsprüfungsfehler wird der durch die Bibliothek angegebene Fehlerstatus von der App erzwungen, wenn die Implementierung der Integritätsprüfung die Einstellung berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="9f31c-161">*Tags* können zum Filtern von Integritätsprüfungen verwendet werden (dies wird im Abschnitt [Filtern von Integritätsprüfungen](#filter-health-checks) genauer beschrieben).</span><span class="sxs-lookup"><span data-stu-id="9f31c-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="9f31c-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> kann auch eine Lambdafunktion ausführen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="9f31c-163">Im folgenden Beispiel wird der Name der Integritätsprüfung als `Example` angegeben, und die Prüfung gibt immer einen fehlerfreien Status zurück:</span><span class="sxs-lookup"><span data-stu-id="9f31c-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

<span data-ttu-id="9f31c-164">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> auf, um Argumente an die Implementierung einer Integritätsprüfung zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="9f31c-164">Call <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddTypeActivatedCheck*> to pass arguments to a health check implementation.</span></span> <span data-ttu-id="9f31c-165">Im folgenden Beispiel akzeptiert `TestHealthCheckWithArgs` eine ganze Zahl und eine Zeichenfolge, wenn <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="9f31c-165">In the following example, `TestHealthCheckWithArgs` accepts an integer and a string for use when <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> is called:</span></span>

```csharp
private class TestHealthCheckWithArgs : IHealthCheck
{
    public TestHealthCheckWithArgs(int i, string s)
    {
        I = i;
        S = s;
    }

    public int I { get; set; }

    public string S { get; set; }

    public Task<HealthCheckResult> CheckHealthAsync(HealthCheckContext context, 
        CancellationToken cancellationToken = default)
    {
        ...
    }
}
```

<span data-ttu-id="9f31c-166">`TestHealthCheckWithArgs` wird durch Aufrufen von `AddTypeActivatedCheck` mit der Übergabe der ganzen Zahl und der Zeichenfolge an die Implementierung registriert:</span><span class="sxs-lookup"><span data-stu-id="9f31c-166">`TestHealthCheckWithArgs` is registered by calling `AddTypeActivatedCheck` with the integer and string passed to the implementation:</span></span>

```csharp
services.AddHealthChecks()
    .AddTypeActivatedCheck<TestHealthCheckWithArgs>(
        "test", 
        failureStatus: HealthStatus.Degraded, 
        tags: new[] { "example" }, 
        args: new object[] { 5, "string" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="9f31c-167">Verwenden von Integritätsprüfungsrouting</span><span class="sxs-lookup"><span data-stu-id="9f31c-167">Use Health Checks Routing</span></span>

<span data-ttu-id="9f31c-168">Rufen Sie in `Startup.Configure` auf der Endpunkterstellung mit der Endpunkt-URL oder dem relativen Pfad `MapHealthChecks` auf:</span><span class="sxs-lookup"><span data-stu-id="9f31c-168">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="9f31c-169">Anfordern des Hosts</span><span class="sxs-lookup"><span data-stu-id="9f31c-169">Require host</span></span>

<span data-ttu-id="9f31c-170">Rufen Sie `RequireHost` auf, um einen oder mehrere zugelassene Hosts für den Integritätsprüfungs-Endpunkt anzugeben.</span><span class="sxs-lookup"><span data-stu-id="9f31c-170">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="9f31c-171">Hosts sollten Unicode anstelle von Punycode verwenden und können einen Port enthalten.</span><span class="sxs-lookup"><span data-stu-id="9f31c-171">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="9f31c-172">Wenn keine Sammlung bereitgestellt wird, wird jeder Host akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-172">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="9f31c-173">Weitere Informationen finden Sie im Abschnitt [Filtern nach Port](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="9f31c-173">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="9f31c-174">Anfordern der Autorisierung</span><span class="sxs-lookup"><span data-stu-id="9f31c-174">Require authorization</span></span>

<span data-ttu-id="9f31c-175">Rufen Sie `RequireAuthorization` auf, um die Autorisierungsmiddleware auf dem Integritätsprüfungs-Anforderungsendpunkt auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-175">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="9f31c-176">Eine `RequireAuthorization`-Überladung akzeptiert eine oder mehrere Autorisierungsrichtlinien.</span><span class="sxs-lookup"><span data-stu-id="9f31c-176">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="9f31c-177">Wenn keine Richtlinie bereitgestellt wird, wird die standardmäßige Autorisierungsrichtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-177">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="9f31c-178">Aktivieren ursprungsübergreifender Anforderungen (CORS)</span><span class="sxs-lookup"><span data-stu-id="9f31c-178">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="9f31c-179">Obwohl das manuelle Ausführen von Integritätsprüfungen über einen Browser kein gängiges Szenario ist, kann CORS-Middleware durch Aufrufen von `RequireCors` für Integritätsprüfungs-Endpunkte aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-179">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="9f31c-180">Eine `RequireCors`-Überladung akzeptiert einen CORS-Richtliniengenerator-Delegaten (`CorsPolicyBuilder`) oder einen Richtliniennamen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-180">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="9f31c-181">Wenn keine Richtlinie bereitgestellt wird, wird die standardmäßige CORS-Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-181">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="9f31c-182">Weitere Informationen finden Sie unter <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="9f31c-182">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="9f31c-183">Optionen für die Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="9f31c-183">Health check options</span></span>

<span data-ttu-id="9f31c-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> ermöglichen die Anpassung des Verhaltens von Integritätsprüfungen:</span><span class="sxs-lookup"><span data-stu-id="9f31c-184"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="9f31c-185">Filtern von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="9f31c-185">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="9f31c-186">Anpassen des HTTP-Statuscodes</span><span class="sxs-lookup"><span data-stu-id="9f31c-186">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="9f31c-187">Unterdrücken von Cacheheadern</span><span class="sxs-lookup"><span data-stu-id="9f31c-187">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="9f31c-188">Anpassen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="9f31c-188">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="9f31c-189">Filtern von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="9f31c-189">Filter health checks</span></span>

<span data-ttu-id="9f31c-190">Standardmäßig führt die Middleware für Integritätsprüfungen alle registrierten Integritätsprüfungen aus.</span><span class="sxs-lookup"><span data-stu-id="9f31c-190">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="9f31c-191">Um eine Teilmenge von Integritätsprüfungen auszuführen, stellen Sie eine Funktion bereit, die einen booleschen Wert an die Option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-191">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="9f31c-192">Im folgenden Beispiel wird die `Bar`-Integritätsprüfung anhand ihres Tags (`bar_tag`) in der Bedingungsanweisung der Funktion herausgefiltert. Dabei wird `true` nur zurückgegeben, wenn die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags>-Eigenschaft der Integritätsprüfung mit `foo_tag` oder `baz_tag` übereinstimmt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-192">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="9f31c-193">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9f31c-193">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="9f31c-194">In `Startup.Configure` filtert `Predicate` die „Bar“-Integritätsprüfung heraus.</span><span class="sxs-lookup"><span data-stu-id="9f31c-194">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="9f31c-195">Nur Foo und Baz werden ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-195">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="9f31c-196">Anpassen des HTTP-Statuscodes</span><span class="sxs-lookup"><span data-stu-id="9f31c-196">Customize the HTTP status code</span></span>

<span data-ttu-id="9f31c-197">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>, um die Zuordnung des Integritätsstatus zu HTTP-Statuscodes anzupassen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-197">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="9f31c-198">Die folgenden <xref:Microsoft.AspNetCore.Http.StatusCodes>-Zuweisungen stellen die von der Middleware verwendeten Standardwerte dar.</span><span class="sxs-lookup"><span data-stu-id="9f31c-198">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="9f31c-199">Ändern Sie die Statuscodewerte so, dass sie Ihren Anforderungen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-199">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="9f31c-200">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9f31c-200">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="9f31c-201">Unterdrücken von Cacheheadern</span><span class="sxs-lookup"><span data-stu-id="9f31c-201">Suppress cache headers</span></span>

<span data-ttu-id="9f31c-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> steuert, ob die Middleware für Integritätsprüfungen einer Testantwort HTTP-Header hinzufügt, um das Zwischenspeichern von Antworten zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="9f31c-202"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="9f31c-203">Wenn der Wert `false` (Standard) lautet, legt die Middleware die Header `Cache-Control`, `Expires` und `Pragma` fest oder überschreibt sie, um eine Zwischenspeicherung der Antworten zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="9f31c-203">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="9f31c-204">Wenn der Wert `true` lautet, ändert die Middleware die Cacheheader der Antwort nicht.</span><span class="sxs-lookup"><span data-stu-id="9f31c-204">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="9f31c-205">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9f31c-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="9f31c-206">Anpassen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="9f31c-206">Customize output</span></span>

<span data-ttu-id="9f31c-207">Legen Sie in `Startup.Configure` die Option [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) auf einen Delegat fest, um die Antwort zu schreiben:</span><span class="sxs-lookup"><span data-stu-id="9f31c-207">In `Startup.Configure`, set the [HealthCheckOptions.ResponseWriter](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter) option to a delegate for writing the response:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="9f31c-208">Der Standarddelegat schreibt eine minimale Klartextantwort mit dem Zeichenfolgenwert [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="9f31c-208">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="9f31c-209">Der folgende benutzerdefinierte Delegat gibt eine benutzerdefinierte JSON-Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="9f31c-209">The following custom delegates output a custom JSON response.</span></span>

<span data-ttu-id="9f31c-210">Das erste Beispiel aus der Beispiel-App veranschaulicht die Verwendung von <xref:System.Text.Json?displayProperty=fullName>:</span><span class="sxs-lookup"><span data-stu-id="9f31c-210">The first example from the sample app demonstrates how to use <xref:System.Text.Json?displayProperty=fullName>:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_SystemTextJson)]

<span data-ttu-id="9f31c-211">Im zweiten Beispiel wird veranschaulicht, wie [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="9f31c-211">The second example demonstrates how to use [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse_NewtonSoftJson)]

<span data-ttu-id="9f31c-212">Kommentieren Sie in der Beispiel-App die [Präprozessordirektive](xref:index#preprocessor-directives-in-sample-code) `SYSTEM_TEXT_JSON` in *CustomWriterStartup.cs* aus, um die `Newtonsoft.Json`-Version von `WriteResponse` zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="9f31c-212">In the sample app, comment out the `SYSTEM_TEXT_JSON` [preprocessor directive](xref:index#preprocessor-directives-in-sample-code) in *CustomWriterStartup.cs* to enable the `Newtonsoft.Json` version of `WriteResponse`.</span></span>

<span data-ttu-id="9f31c-213">Die Integritätsprüfungs-API bietet keine integrierte Unterstützung für komplexe JSON-Rückgabeformate, da das Format für Ihre Überwachungssystemauswahl spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="9f31c-213">The health checks API doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="9f31c-214">Passen Sie die Antwort in den vorangehenden Beispielen nach Bedarf an.</span><span class="sxs-lookup"><span data-stu-id="9f31c-214">Customize the response in the preceding examples as needed.</span></span> <span data-ttu-id="9f31c-215">Weitere Informationen zur JSON-Serialisierung mit `System.Text.Json` finden Sie unter [Serialisieren und Deserialisieren von JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span><span class="sxs-lookup"><span data-stu-id="9f31c-215">For more information on JSON serialization with `System.Text.Json`, see [How to serialize and deserialize JSON in .NET](/dotnet/standard/serialization/system-text-json-how-to).</span></span>

## <a name="database-probe"></a><span data-ttu-id="9f31c-216">Datenbanktest</span><span class="sxs-lookup"><span data-stu-id="9f31c-216">Database probe</span></span>

<span data-ttu-id="9f31c-217">Eine Integritätsprüfung kann eine Datenbankabfrage angeben, die als boolescher Test ausgeführt wird, um zu ermitteln, ob die Datenbank normal reagiert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-217">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="9f31c-218">Die Beispiel-App verwendet [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), eine Bibliothek für Integritätsprüfungen für ASP.NET Core-Apps, um eine Integritätsprüfung für eine SQL Server-Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-218">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="9f31c-219">`AspNetCore.Diagnostics.HealthChecks` führt eine `SELECT 1`-Abfrage in der Datenbank aus, um zu bestätigen, dass die Verbindung mit der Datenbank fehlerfrei ist.</span><span class="sxs-lookup"><span data-stu-id="9f31c-219">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="9f31c-220">Wenn Sie die Datenbankverbindung mithilfe einer Abfrage überprüfen, wählen Sie eine Abfrage aus, die eine schnelle Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-220">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="9f31c-221">Bei einer Abfrage besteht immer das Risiko, dass die Datenbank überladen und ihre Leistung beeinträchtigt wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-221">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="9f31c-222">In den meisten Fällen ist es nicht notwendig, eine Testabfrage auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-222">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="9f31c-223">Es genügt zumeist, einfach erfolgreich eine Verbindung mit der Datenbank herzustellen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-223">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="9f31c-224">Wenn Sie eine Abfrage ausführen müssen, wählen Sie eine einfache SELECT-Abfrage aus, wie z.B. `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-224">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="9f31c-225">Fügen Sie einen Paketverweis auf [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="9f31c-225">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="9f31c-226">Geben Sie in der *appsettings.json* -Datei der App eine gültige Zeichenfolge für die Datenbankverbindung an.</span><span class="sxs-lookup"><span data-stu-id="9f31c-226">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="9f31c-227">Die App verwendet eine SQL Server-Datenbank namens `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="9f31c-227">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="9f31c-228">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-228">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f31c-229">Die Beispiel-App ruft die `AddSqlServer`-Methode mit der Verbindungszeichenfolge der Datenbank ( *DbHealthStartup.cs* ) auf:</span><span class="sxs-lookup"><span data-stu-id="9f31c-229">The sample app calls the `AddSqlServer` method with the database's connection string ( *DbHealthStartup.cs* ):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="9f31c-230">Ein Integritätsprüfungs-Endpunkt wird durch Aufrufen von `MapHealthChecks` in `Startup.Configure` erstellt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-230">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="9f31c-231">Um das Szenario für den Datenbanktest mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="9f31c-231">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="9f31c-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-232">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="9f31c-233">Entity Framework Core-DbContext-Test</span><span class="sxs-lookup"><span data-stu-id="9f31c-233">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="9f31c-234">Die `DbContext`-Überprüfung bestätigt, dass die App mit der Datenbank kommunizieren kann, die für einen EF Core-`DbContext` konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="9f31c-234">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="9f31c-235">Die `DbContext` Überprüfung wird in Apps unterstützt, für die gilt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-235">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="9f31c-236">In ihnen wird [Entity Framework Core (EF Core)](/ef/core/) verwendet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-236">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="9f31c-237">Sie enthalten einen Paketverweis auf [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="9f31c-237">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="9f31c-238">`AddDbContextCheck<TContext>` registriert eine Integritätsprüfung für einen `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-238">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="9f31c-239">Der `DbContext` wird als der `TContext` für die Methode bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-239">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="9f31c-240">Eine Überladung ist verfügbar, um den Fehlerstatus, Tags sowie eine benutzerdefinierte Testabfrage zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="9f31c-240">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="9f31c-241">Standardmäßig:</span><span class="sxs-lookup"><span data-stu-id="9f31c-241">By default:</span></span>

* <span data-ttu-id="9f31c-242">`DbContextHealthCheck` ruft die `CanConnectAsync`-Methode von EF Core auf.</span><span class="sxs-lookup"><span data-stu-id="9f31c-242">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="9f31c-243">Sie können festlegen, welcher Vorgang ausgeführt wird, wenn die Integrität mit `AddDbContextCheck`-Methodenüberladungen überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-243">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="9f31c-244">Der Name der Integritätsprüfung ist der Name des `TContext`-Typs.</span><span class="sxs-lookup"><span data-stu-id="9f31c-244">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="9f31c-245">In der Beispiel-App wird `AppDbContext` für `AddDbContextCheck` bereitgestellt und als Dienst in `Startup.ConfigureServices` ( *DbContextHealthStartup.cs* ) registriert:</span><span class="sxs-lookup"><span data-stu-id="9f31c-245">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` ( *DbContextHealthStartup.cs* ):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="9f31c-246">Ein Integritätsprüfungs-Endpunkt wird durch Aufrufen von `MapHealthChecks` in `Startup.Configure` erstellt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-246">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="9f31c-247">Stellen Sie sicher, dass die mit der Verbindungszeichenfolge angegebene Datenbank in der SQL Server-Instanz nicht vorhanden ist, um das `DbContext`-Testszenario mithilfe der Beispiel-App ausführen zu können.</span><span class="sxs-lookup"><span data-stu-id="9f31c-247">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="9f31c-248">Falls die Datenbank vorhanden ist, löschen Sie sie.</span><span class="sxs-lookup"><span data-stu-id="9f31c-248">If the database exists, delete it.</span></span>

<span data-ttu-id="9f31c-249">Führen Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="9f31c-249">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="9f31c-250">Wenn die App ausgeführt wird, überprüfen Sie den Integritätsstatus, indem Sie in einem Browser eine Anforderung an den `/health`-Endpunkt senden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-250">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="9f31c-251">Datenbank und `AppDbContext` sind nicht vorhanden, also sendet die App die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="9f31c-251">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="9f31c-252">Fordern Sie die Beispiel-App auf, die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-252">Trigger the sample app to create the database.</span></span> <span data-ttu-id="9f31c-253">Senden Sie eine `/createdatabase`-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-253">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="9f31c-254">Die App sendet die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="9f31c-254">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="9f31c-255">Senden Sie eine Anforderung an den `/health`-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-255">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="9f31c-256">Datenbank und Kontext sind vorhanden, also sendet die App die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="9f31c-256">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="9f31c-257">Fordern Sie die Beispiel-App auf, die Datenbank zu löschen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-257">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="9f31c-258">Senden Sie eine `/deletedatabase`-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-258">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="9f31c-259">Die App sendet die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="9f31c-259">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="9f31c-260">Senden Sie eine Anforderung an den `/health`-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-260">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="9f31c-261">Die App meldet einen fehlerhaften Status:</span><span class="sxs-lookup"><span data-stu-id="9f31c-261">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="9f31c-262">Separate Tests für Bereitschaft und Lebendigkeit</span><span class="sxs-lookup"><span data-stu-id="9f31c-262">Separate readiness and liveness probes</span></span>

<span data-ttu-id="9f31c-263">In einigen Hostingszenarien wird ein Integritätsprüfungspaar verwendet, bei dem zwischen zwei App-Status unterschieden wird:</span><span class="sxs-lookup"><span data-stu-id="9f31c-263">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="9f31c-264">*Bereitschaft* gibt an, ob die App ordnungsgemäß ausgeführt wird, aber nicht für den Empfang von Anforderungen bereit ist.</span><span class="sxs-lookup"><span data-stu-id="9f31c-264">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="9f31c-265">*Livetest* gibt an, ob eine App abgestürzt ist und neu gestartet werden muss.</span><span class="sxs-lookup"><span data-stu-id="9f31c-265">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="9f31c-266">Betrachten Sie das folgende Beispiel: Eine App muss eine große Konfigurationsdatei herunterladen, bevor sie für die Verarbeitung von Anforderungen bereit ist.</span><span class="sxs-lookup"><span data-stu-id="9f31c-266">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="9f31c-267">Wir möchten nicht, dass die App neu gestartet wird, wenn der erste Download fehlschlägt, da die App mehrmals versuchen kann, die Datei herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-267">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="9f31c-268">Wir verwenden einen *Livetest* , um den Livezustand des Prozesses zu beschreiben. Weitere Überprüfungen werden nicht durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-268">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="9f31c-269">Außerdem soll verhindert werden, dass Anforderungen an die App gesendet werden, bevor die Konfigurationsdatei erfolgreich heruntergeladen wurde.</span><span class="sxs-lookup"><span data-stu-id="9f31c-269">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="9f31c-270">Wir verwenden einen *Bereitschaftstest* , um den Status "nicht bereit" anzugeben, bis der Download erfolgreich durchgeführt wurde und die App für den Empfang von Anforderungen bereit ist.</span><span class="sxs-lookup"><span data-stu-id="9f31c-270">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="9f31c-271">Die Beispiel-App enthält eine Integritätsprüfung, um den Abschluss eines Starttasks mit langer Ausführungsdauer in einem [gehosteten Dienst](xref:fundamentals/host/hosted-services) zu melden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-271">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="9f31c-272">`StartupHostedServiceHealthCheck` macht die Eigenschaft `StartupTaskCompleted` verfügbar, die der gehostete Dienst auf `true` festlegen kann, wenn der Task mit langer Ausführungsdauer abgeschlossen ist ( *StartupHostedServiceHealthCheck.cs* ):</span><span class="sxs-lookup"><span data-stu-id="9f31c-272">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished ( *StartupHostedServiceHealthCheck.cs* ):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="9f31c-273">Der Hintergrundtask mit langer Ausführungsdauer wird von einem [gehosteten Dienst](xref:fundamentals/host/hosted-services) ( *Services/StartupHostedService* ) gestartet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-273">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) ( *Services/StartupHostedService* ).</span></span> <span data-ttu-id="9f31c-274">Nach Abschluss des Tasks wird `StartupHostedServiceHealthCheck.StartupTaskCompleted` auf `true` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-274">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="9f31c-275">Die Integritätsprüfung wird mit <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> zusammen mit dem gehosteten Dienst in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-275">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="9f31c-276">Da der gehostete Dienst die Eigenschaft in der Integritätsprüfung festlegen muss, wird die Integritätsprüfung ebenfalls im Dienstcontainer ( *LivenessProbeStartup.cs* ) registriert:</span><span class="sxs-lookup"><span data-stu-id="9f31c-276">Because the hosted service must set the property on the health check, the health check is also registered in the service container ( *LivenessProbeStartup.cs* ):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="9f31c-277">Ein Integritätsprüfungs-Endpunkt wird durch Aufrufen von `MapHealthChecks` in `Startup.Configure` erstellt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-277">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="9f31c-278">In der Beispiel-App wird der Integritätsprüfungs-Endpunkt erstellt unter:</span><span class="sxs-lookup"><span data-stu-id="9f31c-278">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="9f31c-279">`/health/ready` für die Bereitschaftsprüfung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-279">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="9f31c-280">Die Bereitschaftsprüfung filtert auf Integritätsprüfungen mit dem `ready`-Tag.</span><span class="sxs-lookup"><span data-stu-id="9f31c-280">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="9f31c-281">`/health/live` für die Lebendigkeitsprüfung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-281">`/health/live` for the liveness check.</span></span> <span data-ttu-id="9f31c-282">Die Lebendigkeitsprüfung filtert `StartupHostedServiceHealthCheck` durch Rückgabe von `false` in [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) heraus (weitere Informationen finden Sie unter [Filtern von Integritätsprüfungen](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="9f31c-282">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="9f31c-283">Im folgenden Beispielcode gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9f31c-283">In the following example code:</span></span>

* <span data-ttu-id="9f31c-284">Die Bereitschaftsprüfung verwendet alle registrierten Überprüfungen mit dem Tag „Bereit“.</span><span class="sxs-lookup"><span data-stu-id="9f31c-284">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="9f31c-285">`Predicate` schließt alle Überprüfungen aus und gibt ein „200-Ok“ zurück.</span><span class="sxs-lookup"><span data-stu-id="9f31c-285">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="9f31c-286">Um das Szenario für die Konfiguration von Bereitschafts-/Lebendigkeitsprüfungen mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="9f31c-286">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="9f31c-287">Besuchen Sie `/health/ready` mehrmals in einem Browser, bis 15 Sekunden verstrichen sind.</span><span class="sxs-lookup"><span data-stu-id="9f31c-287">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="9f31c-288">Die Integritätsprüfung meldet während der ersten 15 Sekunden *Fehlerhaft*.</span><span class="sxs-lookup"><span data-stu-id="9f31c-288">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="9f31c-289">Nach 15 Sekunden meldet der Endpunkt *Fehlerfrei* , was darauf hindeutet, dass die Ausführung des Tasks mit langer Ausführungsdauer durch den gehosteten Dienst abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="9f31c-289">After 15 seconds, the endpoint reports *Healthy* , which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="9f31c-290">In diesem Beispiel wird auch ein Herausgeber der Integritätsprüfung erstellt (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Implementierung), der die erste Bereitschaftsprüfung mit einer Verzögerung von zwei Sekunden ausführt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-290">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="9f31c-291">Weitere Informationen finden Sie im Abschnitt [Herausgeber der Integritätsprüfung](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="9f31c-291">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="9f31c-292">Kubernetes-Beispiel</span><span class="sxs-lookup"><span data-stu-id="9f31c-292">Kubernetes example</span></span>

<span data-ttu-id="9f31c-293">Die Verwendung von Bereitschafts- und Lebendigkeitstests ist in Umgebungen wie [Kubernetes](https://kubernetes.io/) sehr nützlich.</span><span class="sxs-lookup"><span data-stu-id="9f31c-293">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="9f31c-294">In Kubernetes muss eine App möglicherweise zeitaufwendige Startaufgaben ausführen, bevor sie Anforderungen wie z.B. das Testen der Verfügbarkeit der zugrunde liegenden Datenbank annehmen kann.</span><span class="sxs-lookup"><span data-stu-id="9f31c-294">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="9f31c-295">Durch Verwendung separater Überprüfungen kann der Orchestrator unterscheiden, ob eine App funktioniert, aber noch nicht bereit ist, oder ob die App nicht gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="9f31c-295">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="9f31c-296">Weitere Informationen zu Bereitschafts- und Lebendigkeitstests in Kubernetes finden Sie in der Kubernetes-Dokumentation unter [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (Konfigurieren von Lebendigkeits- und Bereitschaftstests).</span><span class="sxs-lookup"><span data-stu-id="9f31c-296">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="9f31c-297">Das folgende Beispiel veranschaulicht die Konfiguration eines Bereitschaftstests in Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="9f31c-297">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```yml
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="9f31c-298">Metrikbasierter Test mit einem benutzerdefinierten Antwortwriter</span><span class="sxs-lookup"><span data-stu-id="9f31c-298">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="9f31c-299">Die Beispiel-App veranschaulicht eine Arbeitsspeicher-Integritätsprüfung mit einem benutzerdefinierten Antwortwriter.</span><span class="sxs-lookup"><span data-stu-id="9f31c-299">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="9f31c-300">`MemoryHealthCheck` meldet einen beeinträchtigten Status, wenn die App mehr Arbeitsspeicher verwendet, als für den Schwellenwert festgelegt wurde (1 GB in der Beispiel-App).</span><span class="sxs-lookup"><span data-stu-id="9f31c-300">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="9f31c-301">Das <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> enthält Garbage Collector-Informationen (GC) für die App ( *MemoryHealthCheck.cs* ):</span><span class="sxs-lookup"><span data-stu-id="9f31c-301">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app ( *MemoryHealthCheck.cs* ):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="9f31c-302">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-302">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f31c-303">Die Integritätsprüfung wird nicht durch Übergabe an <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> aktiviert, stattdessen wird `MemoryHealthCheck` als Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-303">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="9f31c-304">Alle bei <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrierten Dienste stehen für die Dienste und Middleware für Integritätsprüfungen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-304">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="9f31c-305">Es wird empfohlen, Integritätsprüfungsdienste als Singleton-Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="9f31c-305">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="9f31c-306">In *CustomWriterStartup.cs* der Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="9f31c-306">In *CustomWriterStartup.cs* of the sample app:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="9f31c-307">Ein Integritätsprüfungs-Endpunkt wird durch Aufrufen von `MapHealthChecks` in `Startup.Configure` erstellt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-307">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="9f31c-308">Ein `WriteResponse`-Delegat wird für die <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter>-Eigenschaft bereitgestellt, um bei der Ausführung der Integritätsprüfung eine benutzerdefinierte JSON-Antwort auszugeben:</span><span class="sxs-lookup"><span data-stu-id="9f31c-308">A `WriteResponse` delegate is provided to the <Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="9f31c-309">Der `WriteResponse`-Delegat formatiert das `CompositeHealthCheckResult` als JSON-Objekt und führt zu folgender JSON-Ausgabe für die Integritätsprüfungsantwort.</span><span class="sxs-lookup"><span data-stu-id="9f31c-309">The `WriteResponse` delegate formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response.</span></span> <span data-ttu-id="9f31c-310">Weitere Informationen finden Sie im Abschnitt [Anpassen der Ausgabe](#customize-output).</span><span class="sxs-lookup"><span data-stu-id="9f31c-310">For more information, see the [Customize output](#customize-output) section.</span></span>

<span data-ttu-id="9f31c-311">Um den metrikbasierten Test mit benutzerdefinierter Ausgabe des Antwortwriters mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="9f31c-311">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="9f31c-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) enthält Szenarien für metrikbasierte Integritätsprüfungen, einschließlich Prüfungen des Datenträgerspeichers und Lebendigkeitsprüfungen mit Maximalwert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-312">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="9f31c-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-313">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="9f31c-314">Filtern nach Port</span><span class="sxs-lookup"><span data-stu-id="9f31c-314">Filter by port</span></span>

<span data-ttu-id="9f31c-315">Rufen Sie `RequireHost` auf `MapHealthChecks` mit einem URL-Muster auf, das einen Port angibt, um die an diesen Port gerichteten Integritätsprüfungsanforderungen einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="9f31c-315">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="9f31c-316">Dieses Vorgehen wird normalerweise in einer Containerumgebung angewendet, um einen Port für Überwachungsdienste verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-316">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="9f31c-317">Die Beispiel-App konfiguriert den Port mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](xref:fundamentals/configuration/index#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="9f31c-317">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables).</span></span> <span data-ttu-id="9f31c-318">Der Port wird in der Datei *launchSettings.json* festgelegt und über eine Umgebungsvariable an den Konfigurationsanbieter übergeben.</span><span class="sxs-lookup"><span data-stu-id="9f31c-318">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="9f31c-319">Sie müssen den Server auch so konfigurieren, dass er am Verwaltungsport auf Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="9f31c-319">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="9f31c-320">Um die Beispiel-App zum Veranschaulichen der Konfiguration des Verwaltungsports zu verwenden, erstellen Sie die Datei *launchSettings.json* in einem *Eigenschaften* -Ordner.</span><span class="sxs-lookup"><span data-stu-id="9f31c-320">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="9f31c-321">Die folgende *Properties/launchSettings.json* -Datei in der Beispiel-App ist in den Projektdateien der Beispiel-App nicht vorhanden und muss manuell erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="9f31c-321">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="9f31c-322">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-322">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f31c-323">Erstellen Sie einen Integritätsprüfungs-Endpunkt durch Aufrufen von `MapHealthChecks` in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-323">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="9f31c-324">In der Beispiel-App gibt ein Aufruf von `RequireHost` auf dem Endpunkt in `Startup.Configure` den Verwaltungsport aus der Konfiguration an:</span><span class="sxs-lookup"><span data-stu-id="9f31c-324">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="9f31c-325">Endpunkte werden in der Beispiel-App in `Startup.Configure` erstellt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-325">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="9f31c-326">Im folgenden Beispielcode gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9f31c-326">In the following example code:</span></span>

* <span data-ttu-id="9f31c-327">Die Bereitschaftsprüfung verwendet alle registrierten Überprüfungen mit dem Tag „Bereit“.</span><span class="sxs-lookup"><span data-stu-id="9f31c-327">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="9f31c-328">`Predicate` schließt alle Überprüfungen aus und gibt ein „200-Ok“ zurück.</span><span class="sxs-lookup"><span data-stu-id="9f31c-328">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="9f31c-329">Sie können vermeiden, die Datei *launchSettings.json* in der Beispiel-App erstellen zu müssen, indem Sie den Verwaltungsport explizit im Code festlegen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-329">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="9f31c-330">Fügen Sie in *Program.cs* an der Stelle, an der <xref:Microsoft.Extensions.Hosting.HostBuilder> erstellt wird, einen Aufruf von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> hinzu, und geben Sie den Endpunkt des Verwaltungsports der App an.</span><span class="sxs-lookup"><span data-stu-id="9f31c-330">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="9f31c-331">Geben Sie in `Configure` von *ManagementPortStartup.cs* den Verwaltungsport mit `RequireHost` an:</span><span class="sxs-lookup"><span data-stu-id="9f31c-331">In `Configure` of *ManagementPortStartup.cs* , specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="9f31c-332">*Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="9f31c-332">*Program.cs* :</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="9f31c-333">*ManagementPortStartup.cs* :</span><span class="sxs-lookup"><span data-stu-id="9f31c-333">*ManagementPortStartup.cs* :</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="9f31c-334">Um das Szenario für die Konfiguration des Verwaltungsports mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="9f31c-334">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="9f31c-335">Verteilen einer Integritätsprüfungsbibliothek</span><span class="sxs-lookup"><span data-stu-id="9f31c-335">Distribute a health check library</span></span>

<span data-ttu-id="9f31c-336">So verteilen Sie eine Integritätsprüfung als Bibliothek:</span><span class="sxs-lookup"><span data-stu-id="9f31c-336">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="9f31c-337">Schreiben Sie eine Integritätsprüfung, die die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>-Schnittstelle als eigenständige Klasse implementiert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-337">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="9f31c-338">Die Klasse kann [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI), Typaktivierung und [benannte Optionen](xref:fundamentals/configuration/options) verwenden, um auf Konfigurationsdaten zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-338">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="9f31c-339">In der Integritätsprüfungslogik von `CheckHealthAsync` gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9f31c-339">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="9f31c-340">`data1` und `data2` werden in der Methode verwendet, um die Integritätsprüfungslogik des Tests auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-340">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="9f31c-341">`AccessViolationException` wird verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-341">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="9f31c-342">Wenn eine <xref:System.AccessViolationException> auftritt, wird der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> mit dem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zurückgegeben, damit Benutzer den Integritätsprüfungs-Fehlerstatus konfigurieren können.</span><span class="sxs-lookup"><span data-stu-id="9f31c-342">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="9f31c-343">Schreiben Sie eine Erweiterungsmethode mit Parametern, die von der nutzenden App in ihrer `Startup.Configure`-Methode aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-343">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="9f31c-344">Nehmen Sie im folgenden Beispiel die folgende Signatur für die Integritätsprüfungsmethode an:</span><span class="sxs-lookup"><span data-stu-id="9f31c-344">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="9f31c-345">Die oben stehende Signatur gibt an, dass `ExampleHealthCheck` weitere Daten benötigt, um die Testlogik für die Integritätsprüfung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="9f31c-345">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="9f31c-346">Die Daten werden für den Delegaten bereitgestellt, der zum Erstellen der Integritätsprüfungsinstanz verwendet wird, wenn die Integritätsprüfung bei einer Erweiterungsmethode registriert wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-346">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="9f31c-347">Im folgenden Beispiel gibt der Aufrufer optional Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="9f31c-347">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="9f31c-348">Name der Integritätsprüfung (`name`).</span><span class="sxs-lookup"><span data-stu-id="9f31c-348">health check name (`name`).</span></span> <span data-ttu-id="9f31c-349">Wenn der Wert `null` ist, wird `example_health_check` verwendet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-349">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="9f31c-350">string-Datenpunkt für die Integritätsprüfung (`data1`).</span><span class="sxs-lookup"><span data-stu-id="9f31c-350">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="9f31c-351">integer-Datenpunkt für die Integritätsprüfung (`data2`).</span><span class="sxs-lookup"><span data-stu-id="9f31c-351">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="9f31c-352">Wenn der Wert `null` ist, wird `1` verwendet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-352">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="9f31c-353">Fehlerstatus (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="9f31c-353">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="9f31c-354">Der Standardwert ist `null`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-354">The default is `null`.</span></span> <span data-ttu-id="9f31c-355">Wenn `null`, wird [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) als Fehlerstatus gemeldet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-355">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="9f31c-356">Tags (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="9f31c-356">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="9f31c-357">Herausgeber der Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="9f31c-357">Health Check Publisher</span></span>

<span data-ttu-id="9f31c-358">Wenn dem Dienstcontainer ein <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> hinzugefügt wird, führt das Integritätsprüfungssystem Ihre Integritätsprüfungen regelmäßig aus und ruft `PublishAsync` mit dem Ergebnis auf.</span><span class="sxs-lookup"><span data-stu-id="9f31c-358">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="9f31c-359">Dies ist nützlich in einem Szenario mit pushbasiertem Integritätsüberwachungssystem, in dem jeder Prozess das Überwachungssystem regelmäßig aufrufen muss, um die Integrität zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-359">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="9f31c-360">Die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Schnittstelle weist eine einzige Methode auf:</span><span class="sxs-lookup"><span data-stu-id="9f31c-360">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="9f31c-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> ermöglichen Ihnen, Folgendes festzulegen:</span><span class="sxs-lookup"><span data-stu-id="9f31c-361"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="9f31c-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Hierbei handelt es sich um die angewendete anfängliche Verzögerung zwischen dem Start der App und der Ausführung von <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-362"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="9f31c-363">Die Verzögerung wird einmal beim Start angewendet und gilt nicht für die nachfolgenden Iterationen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-363">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="9f31c-364">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-364">The default value is five seconds.</span></span>
* <span data-ttu-id="9f31c-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Hierbei handelt es sich um die Dauer der Ausführung von <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="9f31c-365"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="9f31c-366">Der Standardwert ist 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-366">The default value is 30 seconds.</span></span>
* <span data-ttu-id="9f31c-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Wenn der Wert für <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` ist (Standard), führt der Veröffentlichungsdienst für die Integritätsprüfung alle registrierten Integritätsprüfungen durch.</span><span class="sxs-lookup"><span data-stu-id="9f31c-367"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="9f31c-368">Um eine Teilmenge von Integritätsprüfungen auszuführen, stellen Sie eine Funktion bereit, die die Menge der Prüfungen filtert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-368">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="9f31c-369">Das Prädikat wird in jedem Zeitraum ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-369">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="9f31c-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Hierbei handelt es sich um das Zeitlimit für die Ausführung der Integritätsprüfungen für alle <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-370"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="9f31c-371">Verwenden Sie <xref:System.Threading.Timeout.InfiniteTimeSpan> zur Ausführung ohne Timeout.</span><span class="sxs-lookup"><span data-stu-id="9f31c-371">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="9f31c-372">Der Standardwert ist 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-372">The default value is 30 seconds.</span></span>

<span data-ttu-id="9f31c-373">In der Beispiel-App ist `ReadinessPublisher` eine <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-373">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="9f31c-374">Der Integritätsprüfungsstatus wird für jede Überprüfung auf folgenden Protokollebenen protokolliert:</span><span class="sxs-lookup"><span data-stu-id="9f31c-374">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="9f31c-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), wenn der Status der Integritätsprüfungen <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> lautet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-375">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="9f31c-376">Fehler (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), wenn der Status <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> oder <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> lautet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-376">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="9f31c-377">Im Beispiel `LivenessProbeStartup` der Beispiel-App `StartupHostedService` hat die Bereitschaftsprüfung eine Startverzögerung von zwei Sekunden und führt die Prüfung alle 30 Sekunden aus.</span><span class="sxs-lookup"><span data-stu-id="9f31c-377">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="9f31c-378">Zum Aktivieren der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Implementierung registriert das Beispiel `ReadinessPublisher` als Singletondienst im [Abhängigkeitsinjektionscontainer (Dependency Injection, DI)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="9f31c-378">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="9f31c-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) enthält Herausgeber für verschiedene Systeme, einschließlich [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="9f31c-379">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="9f31c-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-380">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="9f31c-381">Einschränken von Integritätsprüfungen mit MapWhen</span><span class="sxs-lookup"><span data-stu-id="9f31c-381">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="9f31c-382">Verwenden Sie <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>, um die Anforderungspipeline für Endpunkte von Integritätsprüfungen bedingt zu branchen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-382">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="9f31c-383">Im folgenden Beispiel verzweigt `MapWhen` die Anforderungspipeline so, dass die Middleware für Integritätsprüfungen aktiviert wird, wenn eine GET-Anforderung für den Endpunkt `api/HealthCheck` empfangen wird:</span><span class="sxs-lookup"><span data-stu-id="9f31c-383">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="9f31c-384">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span><span class="sxs-lookup"><span data-stu-id="9f31c-384">For more information, see <xref:fundamentals/middleware/index#branch-the-middleware-pipeline>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9f31c-385">ASP.NET Core bietet Middleware für Integritätsprüfungen und Bibliotheken für die Berichterstellung für Komponenten der App-Infrastruktur.</span><span class="sxs-lookup"><span data-stu-id="9f31c-385">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="9f31c-386">Integritätsprüfungen werden von einer App als HTTP-Endpunkte verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="9f31c-386">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="9f31c-387">Integritätsprüfungs-Endpunkte können für eine Vielzahl von Echtzeit-Überwachungsszenarien konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="9f31c-387">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="9f31c-388">Integritätstests können von Containerorchestratoren und Lastenausgleichsmodulen verwendet werden, um den Status einer App zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-388">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="9f31c-389">Ein Containerorchestrator kann z.B. auf eine fehlerhafte Integritätsprüfung reagieren, indem die Ausführung einer Bereitstellung angehalten oder ein Container neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-389">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="9f31c-390">Ein Lastenausgleichsmodul kann auf eine fehlerhafte App reagieren, indem Datenverkehr von der fehlerhaften zu einer fehlerfreien Instanz umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-390">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="9f31c-391">Die Nutzung von Arbeitsspeicher, Datenträgern und anderen physischen Serverressourcen kann auf einen fehlerfreien Status überwacht werden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-391">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="9f31c-392">Integritätsprüfungen können die Abhängigkeiten einer App testen, wie z.B. Datenbanken und externe Dienstendpunkte, um Verfügbarkeit und normale Funktionsweise zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-392">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="9f31c-393">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9f31c-393">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="9f31c-394">Die Beispiel-App enthält Beispiele der Szenarien, die in diesem Thema beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-394">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="9f31c-395">Um die Beispiel-App für ein bestimmtes Szenario auszuführen, verwenden Sie den Befehl [dotnet run](/dotnet/core/tools/dotnet-run) aus dem Ordner des Projekts in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="9f31c-395">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="9f31c-396">Informationen zur Verwendung der Beispiel-App finden Sie in der Datei *README.md* der Beispiel-App und den Szenariobeschreibungen in diesem Thema.</span><span class="sxs-lookup"><span data-stu-id="9f31c-396">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9f31c-397">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="9f31c-397">Prerequisites</span></span>

<span data-ttu-id="9f31c-398">Integritätsprüfungen werden in der Regel mit einem externen Überwachungsdienst oder Containerorchestrator verwendet, um den Status einer App zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-398">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="9f31c-399">Bevor Sie Integritätsprüfungen zu einer App hinzufügen, entscheiden Sie, welches Überwachungssystem verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="9f31c-399">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="9f31c-400">Das Überwachungssystem bestimmt, welche Arten von Integritätsprüfungen erstellt und wie die jeweiligen Endpunkte konfiguriert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-400">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="9f31c-401">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) hinzu.</span><span class="sxs-lookup"><span data-stu-id="9f31c-401">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="9f31c-402">Die Beispiel-App stellt Startcode bereit, um die Integritätsprüfungen für verschiedene Szenarien zu veranschaulichen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-402">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="9f31c-403">Das Szenario [Datenbanktest](#database-probe) überprüft die Integrität einer Datenbankverbindung mithilfe von [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="9f31c-403">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="9f31c-404">Das Szenario [DbContext-Test](#entity-framework-core-dbcontext-probe) überprüft eine Datenbank mithilfe eines EF Core-`DbContext`-Elements.</span><span class="sxs-lookup"><span data-stu-id="9f31c-404">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="9f31c-405">Für ein Erkunden der Datenbankszenarios gilt für die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="9f31c-405">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="9f31c-406">Sie erstellt eine Datenbank und stellt deren Verbindungszeichenfolge in der *appsettings.json* -Datei bereit.</span><span class="sxs-lookup"><span data-stu-id="9f31c-406">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="9f31c-407">Sie hat die folgenden Paketverweise in ihrer Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="9f31c-407">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="9f31c-408">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="9f31c-408">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="9f31c-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="9f31c-409">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="9f31c-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-410">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="9f31c-411">Ein anderes Szenario für Integritätsprüfungen zeigt, wie Sie Integritätsprüfungen auf einen Verwaltungsport filtern können.</span><span class="sxs-lookup"><span data-stu-id="9f31c-411">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="9f31c-412">Für die Beispiel-App müssen Sie eine *Properties/launchSettings.json* -Datei erstellen, die die Verwaltungs-URL und den Verwaltungsport enthält.</span><span class="sxs-lookup"><span data-stu-id="9f31c-412">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="9f31c-413">Weitere Informationen finden Sie im Abschnitt [Filtern nach Port](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="9f31c-413">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="9f31c-414">Grundlegender Integritätstest</span><span class="sxs-lookup"><span data-stu-id="9f31c-414">Basic health probe</span></span>

<span data-ttu-id="9f31c-415">In vielen Apps genügt zur Ermittlung des App-Status eine grundlegende Integritätstestkonfiguration, die die Verfügbarkeit der App für die Verarbeitung von Anforderungen ( *Lebendigkeit* ) meldet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-415">For many apps, a basic health probe configuration that reports the app's availability to process requests ( *liveness* ) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="9f31c-416">Die grundlegende Konfiguration registriert Integritätsprüfungsdienste und ruft die Middleware für Integritätsprüfungen auf, damit diese an einem URL-Endpunkt mit dem Integritätsstatus antwortet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-416">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="9f31c-417">Standardmäßig werden keine spezifischen Integritätsprüfungen registriert, um eine bestimmte Abhängigkeit oder ein bestimmtes Subsystem zu testen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-417">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="9f31c-418">Die App wird als fehlerfrei angesehen, wenn sie an der URL des Integritätsendpunkts antworten kann.</span><span class="sxs-lookup"><span data-stu-id="9f31c-418">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="9f31c-419">Der standardmäßige Antwortwriter schreibt den Status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) als Klartextantwort zurück an den Client und gibt den Status als [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) oder [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) an.</span><span class="sxs-lookup"><span data-stu-id="9f31c-419">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="9f31c-420">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-420">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f31c-421">Fügen Sie mit <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in der Anforderungsverarbeitungspipeline von `Startup.Configure` einen Endpunkt für die Middleware für Integritätsprüfungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="9f31c-421">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="9f31c-422">In der Beispiel-App wird der Integritätsprüfungs-Endpunkt in `/health` ( *BasicStartup.cs* ) erstellt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-422">In the sample app, the health check endpoint is created at `/health` ( *BasicStartup.cs* ):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="9f31c-423">Um das Szenario für die grundlegende Konfiguration mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="9f31c-423">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="9f31c-424">Docker-Beispiel</span><span class="sxs-lookup"><span data-stu-id="9f31c-424">Docker example</span></span>

<span data-ttu-id="9f31c-425">[Docker](xref:host-and-deploy/docker/index) bietet eine integrierte `HEALTHCHECK`-Direktive, mit der Sie den Status einer App überprüfen können, die die grundlegende Konfiguration für Integritätsprüfungen verwendet:</span><span class="sxs-lookup"><span data-stu-id="9f31c-425">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="9f31c-426">Erstellen von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="9f31c-426">Create health checks</span></span>

<span data-ttu-id="9f31c-427">Integritätsprüfungen werden durch Implementieren der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>-Schnittstelle erstellt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-427">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="9f31c-428">Die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>-Methode gibt ein <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zurück, das die Integrität als `Healthy`, `Degraded` oder `Unhealthy` angibt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-428">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="9f31c-429">Das Ergebnis wird als Klartextantwort mit einem konfigurierbaren Statuscode geschrieben (diese Konfiguration wird im Abschnitt [Optionen für die Integritätsprüfung](#health-check-options) beschrieben).</span><span class="sxs-lookup"><span data-stu-id="9f31c-429">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="9f31c-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> kann auch optionale Schlüssel-Wert-Paare zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="9f31c-430"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="9f31c-431">Beispiel für eine Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="9f31c-431">Example health check</span></span>

<span data-ttu-id="9f31c-432">Die folgende `ExampleHealthCheck`-Klasse veranschaulicht das Layout einer Integritätsprüfung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-432">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="9f31c-433">Die Integritätsprüfungslogik wird in der `CheckHealthAsync`-Methode platziert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-433">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="9f31c-434">Im folgenden Beispiel wird eine Dummyvariable `healthCheckResultHealthy` auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-434">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="9f31c-435">Wenn der Wert von `healthCheckResultHealthy` auf `false` festgelegt ist, wird der [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*)-Status zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="9f31c-435">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="9f31c-436">Registrieren von Integritätsprüfungsdiensten</span><span class="sxs-lookup"><span data-stu-id="9f31c-436">Register health check services</span></span>

<span data-ttu-id="9f31c-437">Der `ExampleHealthCheck`-Typ wird den Integritätsprüfungsdiensten in `Startup.ConfigureServices` mit <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-437">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="9f31c-438">Die in der folgenden Abbildung gezeigte <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>-Überladung legt den Fehlerstatus (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) fest, der angegeben werden soll, wenn die Integritätsprüfung einen Fehler meldet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-438">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="9f31c-439">Wenn der Fehlerstatus auf `null` (Standardwert) festgelegt ist, wird [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) gemeldet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-439">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="9f31c-440">Diese Überladung ist ein nützliches Szenario für Bibliotheksersteller: Bei einem Integritätsprüfungsfehler wird der durch die Bibliothek angegebene Fehlerstatus von der App erzwungen, wenn die Implementierung der Integritätsprüfung die Einstellung berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-440">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="9f31c-441">*Tags* können zum Filtern von Integritätsprüfungen verwendet werden (dies wird im Abschnitt [Filtern von Integritätsprüfungen](#filter-health-checks) genauer beschrieben).</span><span class="sxs-lookup"><span data-stu-id="9f31c-441">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="9f31c-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> kann auch eine Lambdafunktion ausführen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-442"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="9f31c-443">Im folgenden `Startup.ConfigureServices`-Beispiel wird der Name der Integritätsprüfung als `Example` angegeben, und die Prüfung gibt immer einen fehlerfreien Status zurück:</span><span class="sxs-lookup"><span data-stu-id="9f31c-443">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="9f31c-444">Verwenden von Middleware für Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="9f31c-444">Use Health Checks Middleware</span></span>

<span data-ttu-id="9f31c-445">Rufen Sie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in der Verarbeitungspipeline von `Startup.Configure` mit der Endpunkt-URL oder dem relativen Pfad auf:</span><span class="sxs-lookup"><span data-stu-id="9f31c-445">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="9f31c-446">Wenn die Integritätsprüfungen an einem bestimmten Port lauschen sollen, verwenden Sie eine Überladung von <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>, um den Port festzulegen (dies wird im Abschnitt [Filtern nach Port](#filter-by-port) genauer beschrieben):</span><span class="sxs-lookup"><span data-stu-id="9f31c-446">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="9f31c-447">Optionen für die Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="9f31c-447">Health check options</span></span>

<span data-ttu-id="9f31c-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> ermöglichen die Anpassung des Verhaltens von Integritätsprüfungen:</span><span class="sxs-lookup"><span data-stu-id="9f31c-448"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="9f31c-449">Filtern von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="9f31c-449">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="9f31c-450">Anpassen des HTTP-Statuscodes</span><span class="sxs-lookup"><span data-stu-id="9f31c-450">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="9f31c-451">Unterdrücken von Cacheheadern</span><span class="sxs-lookup"><span data-stu-id="9f31c-451">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="9f31c-452">Anpassen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="9f31c-452">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="9f31c-453">Filtern von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="9f31c-453">Filter health checks</span></span>

<span data-ttu-id="9f31c-454">Standardmäßig führt die Middleware für Integritätsprüfungen alle registrierten Integritätsprüfungen aus.</span><span class="sxs-lookup"><span data-stu-id="9f31c-454">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="9f31c-455">Um eine Teilmenge von Integritätsprüfungen auszuführen, stellen Sie eine Funktion bereit, die einen booleschen Wert an die Option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-455">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="9f31c-456">Im folgenden Beispiel wird die `Bar`-Integritätsprüfung anhand ihres Tags (`bar_tag`) in der Bedingungsanweisung der Funktion herausgefiltert. Dabei wird `true` nur zurückgegeben, wenn die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags>-Eigenschaft der Integritätsprüfung mit `foo_tag` oder `baz_tag` übereinstimmt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-456">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="9f31c-457">Anpassen des HTTP-Statuscodes</span><span class="sxs-lookup"><span data-stu-id="9f31c-457">Customize the HTTP status code</span></span>

<span data-ttu-id="9f31c-458">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>, um die Zuordnung des Integritätsstatus zu HTTP-Statuscodes anzupassen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-458">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="9f31c-459">Die folgenden <xref:Microsoft.AspNetCore.Http.StatusCodes>-Zuweisungen stellen die von der Middleware verwendeten Standardwerte dar.</span><span class="sxs-lookup"><span data-stu-id="9f31c-459">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="9f31c-460">Ändern Sie die Statuscodewerte so, dass sie Ihren Anforderungen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-460">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="9f31c-461">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9f31c-461">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="9f31c-462">Unterdrücken von Cacheheadern</span><span class="sxs-lookup"><span data-stu-id="9f31c-462">Suppress cache headers</span></span>

<span data-ttu-id="9f31c-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> steuert, ob die Middleware für Integritätsprüfungen einer Testantwort HTTP-Header hinzufügt, um das Zwischenspeichern von Antworten zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="9f31c-463"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="9f31c-464">Wenn der Wert `false` (Standard) lautet, legt die Middleware die Header `Cache-Control`, `Expires` und `Pragma` fest oder überschreibt sie, um eine Zwischenspeicherung der Antworten zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="9f31c-464">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="9f31c-465">Wenn der Wert `true` lautet, ändert die Middleware die Cacheheader der Antwort nicht.</span><span class="sxs-lookup"><span data-stu-id="9f31c-465">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="9f31c-466">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9f31c-466">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="9f31c-467">Anpassen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="9f31c-467">Customize output</span></span>

<span data-ttu-id="9f31c-468">Mit der Option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> wird ein Delegat abgerufen oder festgelegt, der zum Schreiben der Antwort verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-468">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="9f31c-469">Der Standarddelegat schreibt eine minimale Klartextantwort mit dem Zeichenfolgenwert [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="9f31c-469">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="9f31c-470">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="9f31c-470">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="9f31c-471">Der Standarddelegat schreibt eine minimale Klartextantwort mit dem Zeichenfolgenwert [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="9f31c-471">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="9f31c-472">Der folgende benutzerdefinierte Delegat, `WriteResponse`, gibt eine benutzerdefinierte JSON-Antwort aus:</span><span class="sxs-lookup"><span data-stu-id="9f31c-472">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="9f31c-473">Das Integritätsprüfungssystem bietet keine integrierte Unterstützung für komplexe JSON-Rückgabeformate, da das Format für Ihre Überwachungssystemauswahl spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="9f31c-473">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="9f31c-474">Sie können das `JObject` im vorherigen Beispiel nach Bedarf anpassen, sodass Ihre Anforderungen erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-474">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="9f31c-475">Datenbanktest</span><span class="sxs-lookup"><span data-stu-id="9f31c-475">Database probe</span></span>

<span data-ttu-id="9f31c-476">Eine Integritätsprüfung kann eine Datenbankabfrage angeben, die als boolescher Test ausgeführt wird, um zu ermitteln, ob die Datenbank normal reagiert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-476">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="9f31c-477">Die Beispiel-App verwendet [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), eine Bibliothek für Integritätsprüfungen für ASP.NET Core-Apps, um eine Integritätsprüfung für eine SQL Server-Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-477">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="9f31c-478">`AspNetCore.Diagnostics.HealthChecks` führt eine `SELECT 1`-Abfrage in der Datenbank aus, um zu bestätigen, dass die Verbindung mit der Datenbank fehlerfrei ist.</span><span class="sxs-lookup"><span data-stu-id="9f31c-478">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="9f31c-479">Wenn Sie die Datenbankverbindung mithilfe einer Abfrage überprüfen, wählen Sie eine Abfrage aus, die eine schnelle Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-479">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="9f31c-480">Bei einer Abfrage besteht immer das Risiko, dass die Datenbank überladen und ihre Leistung beeinträchtigt wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-480">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="9f31c-481">In den meisten Fällen ist es nicht notwendig, eine Testabfrage auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-481">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="9f31c-482">Es genügt zumeist, einfach erfolgreich eine Verbindung mit der Datenbank herzustellen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-482">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="9f31c-483">Wenn Sie eine Abfrage ausführen müssen, wählen Sie eine einfache SELECT-Abfrage aus, wie z.B. `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-483">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="9f31c-484">Fügen Sie einen Paketverweis auf [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="9f31c-484">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="9f31c-485">Geben Sie in der *appsettings.json* -Datei der App eine gültige Zeichenfolge für die Datenbankverbindung an.</span><span class="sxs-lookup"><span data-stu-id="9f31c-485">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="9f31c-486">Die App verwendet eine SQL Server-Datenbank namens `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="9f31c-486">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="9f31c-487">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-487">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f31c-488">Die Beispiel-App ruft die `AddSqlServer`-Methode mit der Verbindungszeichenfolge der Datenbank ( *DbHealthStartup.cs* ) auf:</span><span class="sxs-lookup"><span data-stu-id="9f31c-488">The sample app calls the `AddSqlServer` method with the database's connection string ( *DbHealthStartup.cs* ):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="9f31c-489">Rufen Sie die Middleware für Integritätsprüfungen in der App-Verarbeitungspipeline in `Startup.Configure` auf:</span><span class="sxs-lookup"><span data-stu-id="9f31c-489">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="9f31c-490">Um das Szenario für den Datenbanktest mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="9f31c-490">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="9f31c-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-491">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="9f31c-492">Entity Framework Core-DbContext-Test</span><span class="sxs-lookup"><span data-stu-id="9f31c-492">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="9f31c-493">Die `DbContext`-Überprüfung bestätigt, dass die App mit der Datenbank kommunizieren kann, die für einen EF Core-`DbContext` konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="9f31c-493">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="9f31c-494">Die `DbContext` Überprüfung wird in Apps unterstützt, für die gilt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-494">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="9f31c-495">In ihnen wird [Entity Framework Core (EF Core)](/ef/core/) verwendet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-495">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="9f31c-496">Sie enthalten einen Paketverweis auf [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="9f31c-496">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="9f31c-497">`AddDbContextCheck<TContext>` registriert eine Integritätsprüfung für einen `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-497">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="9f31c-498">Der `DbContext` wird als der `TContext` für die Methode bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-498">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="9f31c-499">Eine Überladung ist verfügbar, um den Fehlerstatus, Tags sowie eine benutzerdefinierte Testabfrage zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="9f31c-499">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="9f31c-500">Standardmäßig:</span><span class="sxs-lookup"><span data-stu-id="9f31c-500">By default:</span></span>

* <span data-ttu-id="9f31c-501">`DbContextHealthCheck` ruft die `CanConnectAsync`-Methode von EF Core auf.</span><span class="sxs-lookup"><span data-stu-id="9f31c-501">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="9f31c-502">Sie können festlegen, welcher Vorgang ausgeführt wird, wenn die Integrität mit `AddDbContextCheck`-Methodenüberladungen überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-502">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="9f31c-503">Der Name der Integritätsprüfung ist der Name des `TContext`-Typs.</span><span class="sxs-lookup"><span data-stu-id="9f31c-503">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="9f31c-504">In der Beispiel-App wird `AppDbContext` für `AddDbContextCheck` bereitgestellt und als Dienst in `Startup.ConfigureServices` ( *DbContextHealthStartup.cs* ) registriert:</span><span class="sxs-lookup"><span data-stu-id="9f31c-504">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` ( *DbContextHealthStartup.cs* ):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="9f31c-505">In der Beispiel-App fügt `UseHealthChecks` die Middleware für Integritätsprüfungen in `Startup.Configure` hinzu.</span><span class="sxs-lookup"><span data-stu-id="9f31c-505">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="9f31c-506">Stellen Sie sicher, dass die mit der Verbindungszeichenfolge angegebene Datenbank in der SQL Server-Instanz nicht vorhanden ist, um das `DbContext`-Testszenario mithilfe der Beispiel-App ausführen zu können.</span><span class="sxs-lookup"><span data-stu-id="9f31c-506">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="9f31c-507">Falls die Datenbank vorhanden ist, löschen Sie sie.</span><span class="sxs-lookup"><span data-stu-id="9f31c-507">If the database exists, delete it.</span></span>

<span data-ttu-id="9f31c-508">Führen Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="9f31c-508">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="9f31c-509">Wenn die App ausgeführt wird, überprüfen Sie den Integritätsstatus, indem Sie in einem Browser eine Anforderung an den `/health`-Endpunkt senden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-509">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="9f31c-510">Datenbank und `AppDbContext` sind nicht vorhanden, also sendet die App die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="9f31c-510">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="9f31c-511">Fordern Sie die Beispiel-App auf, die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-511">Trigger the sample app to create the database.</span></span> <span data-ttu-id="9f31c-512">Senden Sie eine `/createdatabase`-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-512">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="9f31c-513">Die App sendet die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="9f31c-513">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="9f31c-514">Senden Sie eine Anforderung an den `/health`-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-514">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="9f31c-515">Datenbank und Kontext sind vorhanden, also sendet die App die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="9f31c-515">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="9f31c-516">Fordern Sie die Beispiel-App auf, die Datenbank zu löschen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-516">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="9f31c-517">Senden Sie eine `/deletedatabase`-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-517">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="9f31c-518">Die App sendet die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="9f31c-518">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="9f31c-519">Senden Sie eine Anforderung an den `/health`-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-519">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="9f31c-520">Die App meldet einen fehlerhaften Status:</span><span class="sxs-lookup"><span data-stu-id="9f31c-520">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="9f31c-521">Separate Tests für Bereitschaft und Lebendigkeit</span><span class="sxs-lookup"><span data-stu-id="9f31c-521">Separate readiness and liveness probes</span></span>

<span data-ttu-id="9f31c-522">In einigen Hostingszenarien wird ein Integritätsprüfungspaar verwendet, bei dem zwischen zwei App-Status unterschieden wird:</span><span class="sxs-lookup"><span data-stu-id="9f31c-522">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="9f31c-523">*Bereitschaft* gibt an, ob die App ordnungsgemäß ausgeführt wird, aber nicht für den Empfang von Anforderungen bereit ist.</span><span class="sxs-lookup"><span data-stu-id="9f31c-523">*Readiness* indicates if the app is running normally but isn't ready to receive requests.</span></span>
* <span data-ttu-id="9f31c-524">*Livetest* gibt an, ob eine App abgestürzt ist und neu gestartet werden muss.</span><span class="sxs-lookup"><span data-stu-id="9f31c-524">*Liveness* indicates if an app has crashed and must be restarted.</span></span>

<span data-ttu-id="9f31c-525">Betrachten Sie das folgende Beispiel: Eine App muss eine große Konfigurationsdatei herunterladen, bevor sie für die Verarbeitung von Anforderungen bereit ist.</span><span class="sxs-lookup"><span data-stu-id="9f31c-525">Consider the following example: An app must download a large configuration file before it's ready to process requests.</span></span> <span data-ttu-id="9f31c-526">Wir möchten nicht, dass die App neu gestartet wird, wenn der erste Download fehlschlägt, da die App mehrmals versuchen kann, die Datei herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-526">We don't want the app to be restarted if the initial download fails because the app can retry downloading the file several times.</span></span> <span data-ttu-id="9f31c-527">Wir verwenden einen *Livetest* , um den Livezustand des Prozesses zu beschreiben. Weitere Überprüfungen werden nicht durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-527">We use a *liveness probe* to describe the liveness of the process, no additional checks are performed.</span></span> <span data-ttu-id="9f31c-528">Außerdem soll verhindert werden, dass Anforderungen an die App gesendet werden, bevor die Konfigurationsdatei erfolgreich heruntergeladen wurde.</span><span class="sxs-lookup"><span data-stu-id="9f31c-528">We also want to prevent requests from being sent to the app before the configuration file download has succeeded.</span></span> <span data-ttu-id="9f31c-529">Wir verwenden einen *Bereitschaftstest* , um den Status "nicht bereit" anzugeben, bis der Download erfolgreich durchgeführt wurde und die App für den Empfang von Anforderungen bereit ist.</span><span class="sxs-lookup"><span data-stu-id="9f31c-529">We use a *readiness probe* to indicate a "not ready" state until the download succeeds and the app is ready to receive requests.</span></span>

<span data-ttu-id="9f31c-530">Die Beispiel-App enthält eine Integritätsprüfung, um den Abschluss eines Starttasks mit langer Ausführungsdauer in einem [gehosteten Dienst](xref:fundamentals/host/hosted-services) zu melden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-530">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="9f31c-531">`StartupHostedServiceHealthCheck` macht die Eigenschaft `StartupTaskCompleted` verfügbar, die der gehostete Dienst auf `true` festlegen kann, wenn der Task mit langer Ausführungsdauer abgeschlossen ist ( *StartupHostedServiceHealthCheck.cs* ):</span><span class="sxs-lookup"><span data-stu-id="9f31c-531">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished ( *StartupHostedServiceHealthCheck.cs* ):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="9f31c-532">Der Hintergrundtask mit langer Ausführungsdauer wird von einem [gehosteten Dienst](xref:fundamentals/host/hosted-services) ( *Services/StartupHostedService* ) gestartet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-532">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) ( *Services/StartupHostedService* ).</span></span> <span data-ttu-id="9f31c-533">Nach Abschluss des Tasks wird `StartupHostedServiceHealthCheck.StartupTaskCompleted` auf `true` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="9f31c-533">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="9f31c-534">Die Integritätsprüfung wird mit <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> zusammen mit dem gehosteten Dienst in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-534">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="9f31c-535">Da der gehostete Dienst die Eigenschaft in der Integritätsprüfung festlegen muss, wird die Integritätsprüfung ebenfalls im Dienstcontainer ( *LivenessProbeStartup.cs* ) registriert:</span><span class="sxs-lookup"><span data-stu-id="9f31c-535">Because the hosted service must set the property on the health check, the health check is also registered in the service container ( *LivenessProbeStartup.cs* ):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="9f31c-536">Rufen Sie die Middleware für Integritätsprüfungen in der App-Verarbeitungspipeline in `Startup.Configure` auf.</span><span class="sxs-lookup"><span data-stu-id="9f31c-536">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="9f31c-537">In der Beispiel-App werden die Integritätsprüfungs-Endpunkte in `/health/ready` für die Bereitschaftsprüfung und in `/health/live` für die Lebendigkeitsprüfung erstellt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-537">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="9f31c-538">Die Bereitschaftsprüfung filtert auf Integritätsprüfungen mit dem `ready`-Tag.</span><span class="sxs-lookup"><span data-stu-id="9f31c-538">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="9f31c-539">Die Lebendigkeitsprüfung filtert `StartupHostedServiceHealthCheck` durch Rückgabe von `false` in [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) heraus (weitere Informationen finden Sie unter [Filtern von Integritätsprüfungen](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="9f31c-539">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="9f31c-540">Um das Szenario für die Konfiguration von Bereitschafts-/Lebendigkeitsprüfungen mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="9f31c-540">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="9f31c-541">Besuchen Sie `/health/ready` mehrmals in einem Browser, bis 15 Sekunden verstrichen sind.</span><span class="sxs-lookup"><span data-stu-id="9f31c-541">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="9f31c-542">Die Integritätsprüfung meldet während der ersten 15 Sekunden *Fehlerhaft*.</span><span class="sxs-lookup"><span data-stu-id="9f31c-542">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="9f31c-543">Nach 15 Sekunden meldet der Endpunkt *Fehlerfrei* , was darauf hindeutet, dass die Ausführung des Tasks mit langer Ausführungsdauer durch den gehosteten Dienst abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="9f31c-543">After 15 seconds, the endpoint reports *Healthy* , which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="9f31c-544">In diesem Beispiel wird auch ein Herausgeber der Integritätsprüfung erstellt (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Implementierung), der die erste Bereitschaftsprüfung mit einer Verzögerung von zwei Sekunden ausführt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-544">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="9f31c-545">Weitere Informationen finden Sie im Abschnitt [Herausgeber der Integritätsprüfung](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="9f31c-545">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="9f31c-546">Kubernetes-Beispiel</span><span class="sxs-lookup"><span data-stu-id="9f31c-546">Kubernetes example</span></span>

<span data-ttu-id="9f31c-547">Die Verwendung von Bereitschafts- und Lebendigkeitstests ist in Umgebungen wie [Kubernetes](https://kubernetes.io/) sehr nützlich.</span><span class="sxs-lookup"><span data-stu-id="9f31c-547">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="9f31c-548">In Kubernetes muss eine App möglicherweise zeitaufwendige Startaufgaben ausführen, bevor sie Anforderungen wie z.B. das Testen der Verfügbarkeit der zugrunde liegenden Datenbank annehmen kann.</span><span class="sxs-lookup"><span data-stu-id="9f31c-548">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="9f31c-549">Durch Verwendung separater Überprüfungen kann der Orchestrator unterscheiden, ob eine App funktioniert, aber noch nicht bereit ist, oder ob die App nicht gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="9f31c-549">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="9f31c-550">Weitere Informationen zu Bereitschafts- und Lebendigkeitstests in Kubernetes finden Sie in der Kubernetes-Dokumentation unter [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (Konfigurieren von Lebendigkeits- und Bereitschaftstests).</span><span class="sxs-lookup"><span data-stu-id="9f31c-550">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="9f31c-551">Das folgende Beispiel veranschaulicht die Konfiguration eines Bereitschaftstests in Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="9f31c-551">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="9f31c-552">Metrikbasierter Test mit einem benutzerdefinierten Antwortwriter</span><span class="sxs-lookup"><span data-stu-id="9f31c-552">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="9f31c-553">Die Beispiel-App veranschaulicht eine Arbeitsspeicher-Integritätsprüfung mit einem benutzerdefinierten Antwortwriter.</span><span class="sxs-lookup"><span data-stu-id="9f31c-553">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="9f31c-554">`MemoryHealthCheck` meldet einen Fehlerstatus, wenn die App mehr Arbeitsspeicher verwendet, als für den Schwellenwert festgelegt wurde (1 GB in der Beispiel-App).</span><span class="sxs-lookup"><span data-stu-id="9f31c-554">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="9f31c-555">Das <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> enthält Garbage Collector-Informationen (GC) für die App ( *MemoryHealthCheck.cs* ):</span><span class="sxs-lookup"><span data-stu-id="9f31c-555">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app ( *MemoryHealthCheck.cs* ):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="9f31c-556">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-556">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f31c-557">Die Integritätsprüfung wird nicht durch Übergabe an <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> aktiviert, stattdessen wird `MemoryHealthCheck` als Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-557">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="9f31c-558">Alle bei <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrierten Dienste stehen für die Dienste und Middleware für Integritätsprüfungen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-558">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="9f31c-559">Es wird empfohlen, Integritätsprüfungsdienste als Singleton-Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="9f31c-559">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="9f31c-560">In der Beispiel-App ( *CustomWriterStartup.cs* ):</span><span class="sxs-lookup"><span data-stu-id="9f31c-560">In the sample app ( *CustomWriterStartup.cs* ):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="9f31c-561">Rufen Sie die Middleware für Integritätsprüfungen in der App-Verarbeitungspipeline in `Startup.Configure` auf.</span><span class="sxs-lookup"><span data-stu-id="9f31c-561">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="9f31c-562">Ein `WriteResponse`-Delegat wird in der `ResponseWriter`-Eigenschaft angegeben, um eine benutzerdefinierte JSON-Antwort auszugeben, wenn die Integritätsprüfung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="9f31c-562">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="9f31c-563">Die `WriteResponse`-Methode formatiert das `CompositeHealthCheckResult` als JSON-Objekt und führt zu folgender JSON-Ausgabe für die Integritätsprüfungsantwort:</span><span class="sxs-lookup"><span data-stu-id="9f31c-563">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="9f31c-564">Um den metrikbasierten Test mit benutzerdefinierter Ausgabe des Antwortwriters mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="9f31c-564">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="9f31c-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) enthält Szenarien für metrikbasierte Integritätsprüfungen, einschließlich Prüfungen des Datenträgerspeichers und Lebendigkeitsprüfungen mit Maximalwert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-565">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="9f31c-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-566">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="9f31c-567">Filtern nach Port</span><span class="sxs-lookup"><span data-stu-id="9f31c-567">Filter by port</span></span>

<span data-ttu-id="9f31c-568">Wenn <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> mit einem Port aufgerufen wird, werden Anforderungen durch Integritätsprüfungen auf den angegebenen Port beschränkt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-568">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="9f31c-569">Dieses Vorgehen wird normalerweise in einer Containerumgebung angewendet, um einen Port für Überwachungsdienste verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-569">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="9f31c-570">Die Beispiel-App konfiguriert den Port mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="9f31c-570">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="9f31c-571">Der Port wird in der Datei *launchSettings.json* festgelegt und über eine Umgebungsvariable an den Konfigurationsanbieter übergeben.</span><span class="sxs-lookup"><span data-stu-id="9f31c-571">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="9f31c-572">Sie müssen den Server auch so konfigurieren, dass er am Verwaltungsport auf Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="9f31c-572">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="9f31c-573">Um die Beispiel-App zum Veranschaulichen der Konfiguration des Verwaltungsports zu verwenden, erstellen Sie die Datei *launchSettings.json* in einem *Eigenschaften* -Ordner.</span><span class="sxs-lookup"><span data-stu-id="9f31c-573">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="9f31c-574">Die folgende *Properties/launchSettings.json* -Datei in der Beispiel-App ist in den Projektdateien der Beispiel-App nicht vorhanden und muss manuell erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="9f31c-574">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="9f31c-575">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-575">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f31c-576">Der Aufruf von <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> gibt den Verwaltungsport an ( *ManagementPortStartup.cs* ):</span><span class="sxs-lookup"><span data-stu-id="9f31c-576">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port ( *ManagementPortStartup.cs* ):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="9f31c-577">Sie können es vermeiden, die Datei *launchSettings.json* in der Beispiel-App erstellen zu müssen, indem Sie die URLs und den Verwaltungsport explizit im Code festlegen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-577">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="9f31c-578">Fügen Sie in *Program.cs* an der Stelle, an der <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> erstellt wird, einen Aufruf von <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> hinzu, und geben Sie den normalen Antwortendpunkt der App und den Endpunkt des Verwaltungsports an.</span><span class="sxs-lookup"><span data-stu-id="9f31c-578">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="9f31c-579">Geben Sie in *ManagementPortStartup.cs* an der Stelle, an der <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> aufgerufen wird, explizit den Verwaltungsport an.</span><span class="sxs-lookup"><span data-stu-id="9f31c-579">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="9f31c-580">*Program.cs* :</span><span class="sxs-lookup"><span data-stu-id="9f31c-580">*Program.cs* :</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="9f31c-581">*ManagementPortStartup.cs* :</span><span class="sxs-lookup"><span data-stu-id="9f31c-581">*ManagementPortStartup.cs* :</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="9f31c-582">Um das Szenario für die Konfiguration des Verwaltungsports mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="9f31c-582">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="9f31c-583">Verteilen einer Integritätsprüfungsbibliothek</span><span class="sxs-lookup"><span data-stu-id="9f31c-583">Distribute a health check library</span></span>

<span data-ttu-id="9f31c-584">So verteilen Sie eine Integritätsprüfung als Bibliothek:</span><span class="sxs-lookup"><span data-stu-id="9f31c-584">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="9f31c-585">Schreiben Sie eine Integritätsprüfung, die die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>-Schnittstelle als eigenständige Klasse implementiert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-585">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="9f31c-586">Die Klasse kann [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI), Typaktivierung und [benannte Optionen](xref:fundamentals/configuration/options) verwenden, um auf Konfigurationsdaten zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-586">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="9f31c-587">In der Integritätsprüfungslogik von `CheckHealthAsync` gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9f31c-587">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="9f31c-588">`data1` und `data2` werden in der Methode verwendet, um die Integritätsprüfungslogik des Tests auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-588">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="9f31c-589">`AccessViolationException` wird verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-589">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="9f31c-590">Wenn eine <xref:System.AccessViolationException> auftritt, wird der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> mit dem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zurückgegeben, damit Benutzer den Integritätsprüfungs-Fehlerstatus konfigurieren können.</span><span class="sxs-lookup"><span data-stu-id="9f31c-590">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="9f31c-591">Schreiben Sie eine Erweiterungsmethode mit Parametern, die von der nutzenden App in ihrer `Startup.Configure`-Methode aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-591">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="9f31c-592">Nehmen Sie im folgenden Beispiel die folgende Signatur für die Integritätsprüfungsmethode an:</span><span class="sxs-lookup"><span data-stu-id="9f31c-592">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="9f31c-593">Die oben stehende Signatur gibt an, dass `ExampleHealthCheck` weitere Daten benötigt, um die Testlogik für die Integritätsprüfung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="9f31c-593">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="9f31c-594">Die Daten werden für den Delegaten bereitgestellt, der zum Erstellen der Integritätsprüfungsinstanz verwendet wird, wenn die Integritätsprüfung bei einer Erweiterungsmethode registriert wird.</span><span class="sxs-lookup"><span data-stu-id="9f31c-594">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="9f31c-595">Im folgenden Beispiel gibt der Aufrufer optional Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="9f31c-595">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="9f31c-596">Name der Integritätsprüfung (`name`).</span><span class="sxs-lookup"><span data-stu-id="9f31c-596">health check name (`name`).</span></span> <span data-ttu-id="9f31c-597">Wenn der Wert `null` ist, wird `example_health_check` verwendet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-597">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="9f31c-598">string-Datenpunkt für die Integritätsprüfung (`data1`).</span><span class="sxs-lookup"><span data-stu-id="9f31c-598">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="9f31c-599">integer-Datenpunkt für die Integritätsprüfung (`data2`).</span><span class="sxs-lookup"><span data-stu-id="9f31c-599">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="9f31c-600">Wenn der Wert `null` ist, wird `1` verwendet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-600">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="9f31c-601">Fehlerstatus (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="9f31c-601">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="9f31c-602">Der Standardwert ist `null`.</span><span class="sxs-lookup"><span data-stu-id="9f31c-602">The default is `null`.</span></span> <span data-ttu-id="9f31c-603">Wenn `null`, wird [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) als Fehlerstatus gemeldet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-603">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="9f31c-604">Tags (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="9f31c-604">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="9f31c-605">Herausgeber der Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="9f31c-605">Health Check Publisher</span></span>

<span data-ttu-id="9f31c-606">Wenn dem Dienstcontainer ein <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> hinzugefügt wird, führt das Integritätsprüfungssystem Ihre Integritätsprüfungen regelmäßig aus und ruft `PublishAsync` mit dem Ergebnis auf.</span><span class="sxs-lookup"><span data-stu-id="9f31c-606">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="9f31c-607">Dies ist nützlich in einem Szenario mit pushbasiertem Integritätsüberwachungssystem, in dem jeder Prozess das Überwachungssystem regelmäßig aufrufen muss, um die Integrität zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-607">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="9f31c-608">Die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Schnittstelle weist eine einzige Methode auf:</span><span class="sxs-lookup"><span data-stu-id="9f31c-608">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="9f31c-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> ermöglichen Ihnen, Folgendes festzulegen:</span><span class="sxs-lookup"><span data-stu-id="9f31c-609"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="9f31c-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: Hierbei handelt es sich um die angewendete anfängliche Verzögerung zwischen dem Start der App und der Ausführung von <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-610"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>: The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="9f31c-611">Die Verzögerung wird einmal beim Start angewendet und gilt nicht für die nachfolgenden Iterationen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-611">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="9f31c-612">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-612">The default value is five seconds.</span></span>
* <span data-ttu-id="9f31c-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: Hierbei handelt es sich um die Dauer der Ausführung von <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>.</span><span class="sxs-lookup"><span data-stu-id="9f31c-613"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period>: The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="9f31c-614">Der Standardwert ist 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-614">The default value is 30 seconds.</span></span>
* <span data-ttu-id="9f31c-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: Wenn der Wert für <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` ist (Standard), führt der Veröffentlichungsdienst für die Integritätsprüfung alle registrierten Integritätsprüfungen durch.</span><span class="sxs-lookup"><span data-stu-id="9f31c-615"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate>: If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="9f31c-616">Um eine Teilmenge von Integritätsprüfungen auszuführen, stellen Sie eine Funktion bereit, die die Menge der Prüfungen filtert.</span><span class="sxs-lookup"><span data-stu-id="9f31c-616">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="9f31c-617">Das Prädikat wird in jedem Zeitraum ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-617">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="9f31c-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: Hierbei handelt es sich um das Zeitlimit für die Ausführung der Integritätsprüfungen für alle <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-618"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout>: The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="9f31c-619">Verwenden Sie <xref:System.Threading.Timeout.InfiniteTimeSpan> zur Ausführung ohne Timeout.</span><span class="sxs-lookup"><span data-stu-id="9f31c-619">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="9f31c-620">Der Standardwert ist 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-620">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="9f31c-621">Im Release ASP.NET Core 2.2 wird die Einstellung <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> nicht von der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Implementierung berücksichtigt; sie legt den Wert von <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> fest.</span><span class="sxs-lookup"><span data-stu-id="9f31c-621">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="9f31c-622">Dieses Problem wurde in ASP.NET Core 3.0 behandelt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-622">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="9f31c-623">In der Beispiel-App ist `ReadinessPublisher` eine <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="9f31c-623">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="9f31c-624">Der Integritätsprüfungsstatus wird für jede Überprüfung auf folgende Weise protokolliert:</span><span class="sxs-lookup"><span data-stu-id="9f31c-624">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="9f31c-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), wenn der Status der Integritätsprüfungen <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> lautet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-625">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="9f31c-626">Fehler (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), wenn der Status <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> oder <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> lautet.</span><span class="sxs-lookup"><span data-stu-id="9f31c-626">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="9f31c-627">Im Beispiel `LivenessProbeStartup` der Beispiel-App `StartupHostedService` hat die Bereitschaftsprüfung eine Startverzögerung von zwei Sekunden und führt die Prüfung alle 30 Sekunden aus.</span><span class="sxs-lookup"><span data-stu-id="9f31c-627">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="9f31c-628">Zum Aktivieren der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Implementierung registriert das Beispiel `ReadinessPublisher` als Singletondienst im [Abhängigkeitsinjektionscontainer (Dependency Injection, DI)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="9f31c-628">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="9f31c-629">Die folgende Problemumgehung ermöglicht das Hinzufügen einer <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Instanz zum Dienstcontainer, wenn einer oder mehrere andere gehostete Dienste der App bereits hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="9f31c-629">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="9f31c-630">Diese Problemumgehung ist in ASP.NET Core 3.0 nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9f31c-630">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="9f31c-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) enthält Herausgeber für verschiedene Systeme, einschließlich [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="9f31c-631">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="9f31c-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9f31c-632">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="9f31c-633">Einschränken von Integritätsprüfungen mit MapWhen</span><span class="sxs-lookup"><span data-stu-id="9f31c-633">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="9f31c-634">Verwenden Sie <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>, um die Anforderungspipeline für Endpunkte von Integritätsprüfungen bedingt zu branchen.</span><span class="sxs-lookup"><span data-stu-id="9f31c-634">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="9f31c-635">Im folgenden Beispiel verzweigt `MapWhen` die Anforderungspipeline so, dass die Middleware für Integritätsprüfungen aktiviert wird, wenn eine GET-Anforderung für den Endpunkt `api/HealthCheck` empfangen wird:</span><span class="sxs-lookup"><span data-stu-id="9f31c-635">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="9f31c-636">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="9f31c-636">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
