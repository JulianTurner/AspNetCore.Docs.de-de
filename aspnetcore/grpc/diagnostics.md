---
title: Protokollierung und Diagnosen in gRPC für .NET
author: jamesnk
description: Hier erfahren Sie, wie Sie Diagnosen aus Ihrer gRPC-App für .NET erhalten.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
no-loc:
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
uid: grpc/diagnostics
ms.openlocfilehash: 7d2da20d04b93ebcd16fb58a4b74b5b67d37bd72
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722922"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="04aa5-103">Protokollierung und Diagnosen in gRPC für .NET</span><span class="sxs-lookup"><span data-stu-id="04aa5-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="04aa5-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="04aa5-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="04aa5-105">In diesem Artikel erhalten Sie eine Anleitung, wie Sie Diagnosedaten aus einer gRPC-App erhalten, die Sie beim Troubleshooting von Problemen unterstützen.</span><span class="sxs-lookup"><span data-stu-id="04aa5-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="04aa5-106">Folgende Themen werden behandelt:</span><span class="sxs-lookup"><span data-stu-id="04aa5-106">Topics covered include:</span></span>

* <span data-ttu-id="04aa5-107">**Protokollierung:** Strukturierte Protokolle, die in die [.NET Core-Protokollierung](xref:fundamentals/logging/index) geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="04aa5-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="04aa5-108"><xref:Microsoft.Extensions.Logging.ILogger> wird von App-Frameworks verwendet, um Protokolle zu schreiben, sowie von Benutzern für die eigene Protokollierung in einer App.</span><span class="sxs-lookup"><span data-stu-id="04aa5-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="04aa5-109">**Ablaufverfolgung:** Ereignisse im Zusammenhang mit einem Vorgang werden mithilfe von `DiaganosticSource` und `Activity` geschrieben.</span><span class="sxs-lookup"><span data-stu-id="04aa5-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="04aa5-110">Ablaufverfolgungen aus Diagnosequellen werden in der Regel dazu verwendet, um App-Telemetriedaten von Bibliotheken wie [Application Insights](/azure/azure-monitor/app/asp-net-core) und [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) zu sammeln.</span><span class="sxs-lookup"><span data-stu-id="04aa5-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="04aa5-111">**Metriken:** Hierbei handelt es sich um eine Darstellung von Datenmesswerten über bestimmte Zeiträume hinweg, beispielsweise für Anforderungen pro Sekunde.</span><span class="sxs-lookup"><span data-stu-id="04aa5-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="04aa5-112">Metriken werden mithilfe von `EventCounter` ausgegeben und können mithilfe des [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters)-Befehlszeilentools oder mit [Application Insights](/azure/azure-monitor/app/eventcounters) überwacht werden.</span><span class="sxs-lookup"><span data-stu-id="04aa5-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="04aa5-113">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="04aa5-113">Logging</span></span>

<span data-ttu-id="04aa5-114">gRPC-Dienste und der gRPC-Client schreiben Protokolle mithilfe der [.NET Core-Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="04aa5-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="04aa5-115">Protokolle sind gut als Ausgangspunkt geeignet, wenn Sie ein unerwartetes Verhalten Ihrer Apps debuggen müssen.</span><span class="sxs-lookup"><span data-stu-id="04aa5-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="04aa5-116">Protokollierung für gRPC-Dienste</span><span class="sxs-lookup"><span data-stu-id="04aa5-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="04aa5-117">Serverseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="04aa5-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="04aa5-118">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="04aa5-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="04aa5-119">Da gRPC-Dienste bei ASP.NET Core gehostet werden, wird das ASP.NET Core-Protokollierungssystem verwendet.</span><span class="sxs-lookup"><span data-stu-id="04aa5-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="04aa5-120">In der Standardkonfiguration werden für gRPC nur wenige Informationen protokolliert, das kann jedoch konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="04aa5-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="04aa5-121">In der Dokumentation zur [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index#configuration) finden Sie weitere Informationen zum Konfigurieren der ASP.NET Core-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="04aa5-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="04aa5-122">gRPC fügt Protokolle unter der `Grpc`-Kategorie hinzu.</span><span class="sxs-lookup"><span data-stu-id="04aa5-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="04aa5-123">Wenn Sie in gRPC detaillierte Protokolle aktivieren möchten, konfigurieren Sie die `Grpc`-Präfixe auf die `Debug`-Ebene in Ihrer *appsettings.json*-Datei, indem Sie die folgenden Elemente im `LogLevel`-Unterbereich in `Logging` hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="04aa5-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="04aa5-124">Sie können das auch in der Datei *Startup.cs* mit `ConfigureLogging` konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="04aa5-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="04aa5-125">Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie den folgenden Konfigurationswert in Ihrem Konfigurationssystem fest:</span><span class="sxs-lookup"><span data-stu-id="04aa5-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="04aa5-126">Suchen Sie in der Dokumentation nach Informationen zu Ihrem Konfigurationssystem, um zu bestimmen, wie geschachtelte Konfigurationswerte angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="04aa5-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="04aa5-127">Bei der Verwendung von Umgebungsvariablen werden beispielsweise zwei `_`-Zeichen anstelle von `:` verwendet (z. B. `Logging__LogLevel__Grpc`).</span><span class="sxs-lookup"><span data-stu-id="04aa5-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="04aa5-128">Es empfiehlt sich, die `Debug`-Ebene zu verwenden, wenn detailliertere Diagnosedaten Ihrer App gesammelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="04aa5-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="04aa5-129">Die `Trace`-Ebene bietet nur sehr wenige Diagnoseinformationen und wird nur selten verwendet, um App-Probleme zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="04aa5-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="04aa5-130">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="04aa5-130">Sample logging output</span></span>

<span data-ttu-id="04aa5-131">Hier finden Sie ein Beispiel einer Konsolenausgabe auf der `Debug`-Ebene eines gRPC-Diensts:</span><span class="sxs-lookup"><span data-stu-id="04aa5-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a><span data-ttu-id="04aa5-132">Zugreifen auf serverseitige Protokolle</span><span class="sxs-lookup"><span data-stu-id="04aa5-132">Access server-side logs</span></span>

<span data-ttu-id="04aa5-133">Wie Sie auf serverseitige Protokolle zugreifen können, hängt von der Ausführungsumgebung ab.</span><span class="sxs-lookup"><span data-stu-id="04aa5-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="04aa5-134">Ausführung als Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="04aa5-134">As a console app</span></span>

<span data-ttu-id="04aa5-135">Wenn die Ausführung in einer Konsolen-App stattfindet, sollte die [Konsolenprotokollierung](xref:fundamentals/logging/index#console) standardmäßig aktiviert sein.</span><span class="sxs-lookup"><span data-stu-id="04aa5-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="04aa5-136">gRPC-Protokolle werden in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="04aa5-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="04aa5-137">Andere Umgebungen</span><span class="sxs-lookup"><span data-stu-id="04aa5-137">Other environments</span></span>

<span data-ttu-id="04aa5-138">Wenn die App in einer anderen Umgebung bereitgestellt wird (z. B. Docker, Kubernetes oder Windows-Dienst) finden Sie unter <xref:fundamentals/logging/index> weitere Informationen zum Konfigurieren von Protokollierungsanbietern, die für die Umgebung geeignet sind.</span><span class="sxs-lookup"><span data-stu-id="04aa5-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="04aa5-139">gRPC-Clientprotokollierung</span><span class="sxs-lookup"><span data-stu-id="04aa5-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="04aa5-140">Clientseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="04aa5-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="04aa5-141">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="04aa5-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="04aa5-142">Wenn Sie Protokolle vom .NET-Client erhalten möchten, können Sie die `GrpcChannelOptions.LoggerFactory`-Eigenschaft festlegen, wenn der Kanal des Clients erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="04aa5-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="04aa5-143">Wenn Sie einen gRPC-Dienst in einer ASP.NET Core-App aufrufen, kann die Protokollierungsfactory aus einer Abhängigkeitsinjektion (Dependency Injection, DI) aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="04aa5-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="04aa5-144">Eine alternative Möglichkeit, die Clientprotokollierung zu aktivieren, ist die Verwendung der [gRPC-Clientfactory](xref:grpc/clientfactory), um den Client zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="04aa5-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="04aa5-145">Ein für die Clientfactory registrierter und aus der DI aufgelöster gRPC-Client verwendet automatisch die für die App konfigurierte Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="04aa5-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="04aa5-146">Wenn Ihre App die DI nicht verwendet, können Sie mit [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*) eine neue `ILoggerFactory`-Instanz erstellen.</span><span class="sxs-lookup"><span data-stu-id="04aa5-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="04aa5-147">Fügen Sie Ihrer App das Paket [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) hinzu, um auf diese Methode zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="04aa5-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="04aa5-148">Protokollbereiche des gRPC-Clients</span><span class="sxs-lookup"><span data-stu-id="04aa5-148">gRPC client log scopes</span></span>

<span data-ttu-id="04aa5-149">Der gRPC-Client fügt Protokollen, die während eines gRPC-Aufrufs erstellt werden, einen [Protokollierungsbereich](../fundamentals/logging/index.md#log-scopes) hinzu.</span><span class="sxs-lookup"><span data-stu-id="04aa5-149">The gRPC client adds a [logging scope](../fundamentals/logging/index.md#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="04aa5-150">Der Bereich verfügt über mit dem gRPC-Aufruf verbundene Metadaten:</span><span class="sxs-lookup"><span data-stu-id="04aa5-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="04aa5-151">**GrpcMethodType:** Der gRPC-Methodentyp.</span><span class="sxs-lookup"><span data-stu-id="04aa5-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="04aa5-152">Mögliche Werte sind Namen der `Grpc.Core.MethodType`-Enumeration, z. B. Unär.</span><span class="sxs-lookup"><span data-stu-id="04aa5-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="04aa5-153">**GrpcUri:** Der relative URI der gRPC-Methode, z. B. /greet.Greeter/SayHellos.</span><span class="sxs-lookup"><span data-stu-id="04aa5-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="04aa5-154">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="04aa5-154">Sample logging output</span></span>

<span data-ttu-id="04aa5-155">Hier finden Sie ein Beispiel einer Konsolenausgabe auf der `Debug`-Ebene eines gRPC-Clients:</span><span class="sxs-lookup"><span data-stu-id="04aa5-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a><span data-ttu-id="04aa5-156">Ablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="04aa5-156">Tracing</span></span>

<span data-ttu-id="04aa5-157">gRPC-Dienste und der gRPC-Client stellen Informationen zu gRPC-Aufrufen mithilfe von [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) und [Activity](/dotnet/api/system.diagnostics.activity) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="04aa5-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="04aa5-158">.NET gRPC verwendet eine Activity-Klasse, um einen gRPC-Aufruf darzustellen.</span><span class="sxs-lookup"><span data-stu-id="04aa5-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="04aa5-159">Ablaufverfolgungsereignisse werden bei Beginn und Ende der gRPC-Aufrufsaktivität in die Diagnosequelle geschrieben.</span><span class="sxs-lookup"><span data-stu-id="04aa5-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="04aa5-160">Bei der Ablaufverfolgung werden keine Informationen dazu gesammelt, wann Nachrichten während der Lebensdauer eines gRPC-Streamingaufrufs gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="04aa5-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="04aa5-161">gRPC-Dienstablaufsverfolgung</span><span class="sxs-lookup"><span data-stu-id="04aa5-161">gRPC service tracing</span></span>

<span data-ttu-id="04aa5-162">gRPC-Dienste werden bei ASP.NET Core gehostet, wo Ereignisse zu eingehenden HTTP-Anforderungen gemeldet werden.</span><span class="sxs-lookup"><span data-stu-id="04aa5-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="04aa5-163">Für gRPC spezifische Metadaten werden den bestehenden HTTP-Anforderungsdiagnosedaten hinzugefügt, die von ASP.NET Core bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="04aa5-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="04aa5-164">Der Name der Diagnosequelle ist `Microsoft.AspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="04aa5-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="04aa5-165">Der Name der Aktivität ist `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span><span class="sxs-lookup"><span data-stu-id="04aa5-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="04aa5-166">Der Name der gRPC-Methode, die vom gRPC-Aufruf aufgerufen wird, wird als Tag mit dem Namen `grpc.method` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="04aa5-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="04aa5-167">Der Statuscode des gRPC-Aufrufs wird bei Abschluss als Tag mit dem Namen `grpc.status_code` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="04aa5-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="04aa5-168">gPRC-Clientablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="04aa5-168">gRPC client tracing</span></span>

<span data-ttu-id="04aa5-169">Der .NET-gRPC-Client verwendet `HttpClient`, um gRPC-Aufrufe durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="04aa5-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="04aa5-170">Obwohl `HttpClient` Diagnoseereignisse schreibt, stellt der .NET-gRPC-Client eine benutzerdefinierte Diagnosequelle, eine Aktivität und Ereignisse zur Verfügung, sodass alle erforderlichen Informationen zu einem gRPC-Aufruf gesammelt werden können.</span><span class="sxs-lookup"><span data-stu-id="04aa5-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="04aa5-171">Der Name der Diagnosequelle ist `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="04aa5-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="04aa5-172">Der Name der Aktivität ist `Grpc.Net.Client.GrpcOut`.</span><span class="sxs-lookup"><span data-stu-id="04aa5-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="04aa5-173">Der Name der gRPC-Methode, die vom gRPC-Aufruf aufgerufen wird, wird als Tag mit dem Namen `grpc.method` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="04aa5-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="04aa5-174">Der Statuscode des gRPC-Aufrufs wird bei Abschluss als Tag mit dem Namen `grpc.status_code` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="04aa5-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="04aa5-175">Sammeln von Ablaufverfolgungsdaten</span><span class="sxs-lookup"><span data-stu-id="04aa5-175">Collecting tracing</span></span>

<span data-ttu-id="04aa5-176">Die einfachste Möglichkeit, `DiagnosticSource` zu verwenden, ist die Konfiguration einer Telemetriebibliothek wie [Application Insights](/azure/azure-monitor/app/asp-net-core) oder [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="04aa5-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="04aa5-177">Die Bibliothek verarbeitet Informationen zu gRPC-Aufrufen zusammen mit weiteren App-Telemetriedaten.</span><span class="sxs-lookup"><span data-stu-id="04aa5-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="04aa5-178">Die Ablaufverfolgung kann in einem verwalteten Dienst wie Application Insights eingesehen werden, oder Sie entscheiden sich dazu, Ihr eigenes verteiltes Ablaufverfolgungssystem auszuführen.</span><span class="sxs-lookup"><span data-stu-id="04aa5-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="04aa5-179">OpenTelemetry unterstützt das Exportieren von Ablaufverfolgungsdaten zu [Jaeger](https://www.jaegertracing.io/) und [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="04aa5-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="04aa5-180">`DiagnosticSource` kann Ablaufverfolgungsereignisse im Code mithilfe von `DiagnosticListener` verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="04aa5-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="04aa5-181">Weitere Informationen zum Lauschen auf einer Diagnosequelle mit Code finden Sie unter [Verarbeiten von Daten mithilfe von DiagnosticListener](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="04aa5-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="04aa5-182">Telemetriebibliotheken erfassen aktuell keine gRPC-spezifischen `Grpc.Net.Client.GrpcOut`-Telemetriedaten.</span><span class="sxs-lookup"><span data-stu-id="04aa5-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="04aa5-183">Aktuell wird aber daran gearbeitet, die Telemetriebibliotheken zu optimieren, sodass diese Ablaufverfolgung erfasst wird.</span><span class="sxs-lookup"><span data-stu-id="04aa5-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="04aa5-184">Metriken</span><span class="sxs-lookup"><span data-stu-id="04aa5-184">Metrics</span></span>

<span data-ttu-id="04aa5-185">Bei Metriken handelt es sich um eine Darstellung von Datenmesswerten über bestimmte Zeiträume hinweg, beispielsweise für Anforderungen pro Sekunde.</span><span class="sxs-lookup"><span data-stu-id="04aa5-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="04aa5-186">Metrikdaten ermöglichen die Überwachung des Zustands einer App auf einer hohen Ebene.</span><span class="sxs-lookup"><span data-stu-id="04aa5-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="04aa5-187">.NET-gRPC-Metriken werden mithilfe von `EventCounter` ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="04aa5-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="04aa5-188">gRPC-Dienstmetriken</span><span class="sxs-lookup"><span data-stu-id="04aa5-188">gRPC service metrics</span></span>

<span data-ttu-id="04aa5-189">gRPC-Servermetriken werden in der `Grpc.AspNetCore.Server`-Ereignisquelle gemeldet.</span><span class="sxs-lookup"><span data-stu-id="04aa5-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="04aa5-190">name</span><span class="sxs-lookup"><span data-stu-id="04aa5-190">Name</span></span>                      | <span data-ttu-id="04aa5-191">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04aa5-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="04aa5-192">Aufrufe gesamt</span><span class="sxs-lookup"><span data-stu-id="04aa5-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="04aa5-193">Aktuelle Aufrufe</span><span class="sxs-lookup"><span data-stu-id="04aa5-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="04aa5-194">Insgesamt fehlgeschlagene Aufrufe</span><span class="sxs-lookup"><span data-stu-id="04aa5-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="04aa5-195">Anzahl an Aufrufen, die eine Frist überschritten haben</span><span class="sxs-lookup"><span data-stu-id="04aa5-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="04aa5-196">Gesamtzahl der gesendeten Nachrichten</span><span class="sxs-lookup"><span data-stu-id="04aa5-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="04aa5-197">Insgesamt empfangene Nachrichten</span><span class="sxs-lookup"><span data-stu-id="04aa5-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="04aa5-198">Insgesamt nicht implementierte Aufrufe</span><span class="sxs-lookup"><span data-stu-id="04aa5-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="04aa5-199">In ASP.NET Core stehen auch eigene Metriken für die `Microsoft.AspNetCore.Hosting`-Ereignisquelle zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="04aa5-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="04aa5-200">gRPC-Clientmetriken</span><span class="sxs-lookup"><span data-stu-id="04aa5-200">gRPC client metrics</span></span>

<span data-ttu-id="04aa5-201">gRPC-Clientmetriken werden in der `Grpc.Net.Client`-Ereignisquelle gemeldet.</span><span class="sxs-lookup"><span data-stu-id="04aa5-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="04aa5-202">name</span><span class="sxs-lookup"><span data-stu-id="04aa5-202">Name</span></span>                      | <span data-ttu-id="04aa5-203">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="04aa5-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="04aa5-204">Aufrufe gesamt</span><span class="sxs-lookup"><span data-stu-id="04aa5-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="04aa5-205">Aktuelle Aufrufe</span><span class="sxs-lookup"><span data-stu-id="04aa5-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="04aa5-206">Insgesamt fehlgeschlagene Aufrufe</span><span class="sxs-lookup"><span data-stu-id="04aa5-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="04aa5-207">Anzahl an Aufrufen, die eine Frist überschritten haben</span><span class="sxs-lookup"><span data-stu-id="04aa5-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="04aa5-208">Gesamtzahl der gesendeten Nachrichten</span><span class="sxs-lookup"><span data-stu-id="04aa5-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="04aa5-209">Insgesamt empfangene Nachrichten</span><span class="sxs-lookup"><span data-stu-id="04aa5-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="04aa5-210">Überwachen von Metriken</span><span class="sxs-lookup"><span data-stu-id="04aa5-210">Observe metrics</span></span>

<span data-ttu-id="04aa5-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) ist ein Leistungsüberwachungstool zur Ad-hoc-Überwachung der Integrität und zur Leistungsuntersuchung auf erster Ebene.</span><span class="sxs-lookup"><span data-stu-id="04aa5-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="04aa5-212">Sie können eine .NET-App entweder mit `Grpc.AspNetCore.Server` oder `Grpc.Net.Client` als Anbietername überwachen.</span><span class="sxs-lookup"><span data-stu-id="04aa5-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

<span data-ttu-id="04aa5-213">Eine andere Möglichkeit, gRPC-Metriken zu überwachen, ist es, Indikatoren mithilfe des [Microsoft.ApplicationInsights.EventCounterCollector-Pakets](/azure/azure-monitor/app/eventcounters) von Application Insights zu sammeln.</span><span class="sxs-lookup"><span data-stu-id="04aa5-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="04aa5-214">Sobald Application Insights eingerichtet ist, werden allgemeine .NET-Indikatoren zur Laufzeit gesammelt.</span><span class="sxs-lookup"><span data-stu-id="04aa5-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="04aa5-215">Indikatoren von gRPC werden nicht standardmäßig gesammelt, aber Application Insights kann so [angepasst werden, dass zusätzliche Indikatoren eingeschlossen](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected) werden.</span><span class="sxs-lookup"><span data-stu-id="04aa5-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="04aa5-216">Geben Sie die gRPC-Indikatoren, die gesammelt werden sollen, für Application Insights in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="04aa5-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a><span data-ttu-id="04aa5-217">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="04aa5-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>