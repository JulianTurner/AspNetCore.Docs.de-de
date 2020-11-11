---
title: Protokollierung und Diagnosen in gRPC für .NET
author: jamesnk
description: Hier erfahren Sie, wie Sie Diagnosen aus Ihrer gRPC-App für .NET erhalten.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
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
uid: grpc/diagnostics
ms.openlocfilehash: 1f25ae76e5a480e5e6f247e4ac78d06dd4e778e9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060442"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>Protokollierung und Diagnosen in gRPC für .NET

Von [James Newton-King](https://twitter.com/jamesnk)

In diesem Artikel erhalten Sie eine Anleitung, wie Sie Diagnosedaten aus einer gRPC-App erhalten, die Sie beim Troubleshooting von Problemen unterstützen. Folgende Themen werden behandelt:

* **Protokollierung:** Strukturierte Protokolle, die in die [.NET Core-Protokollierung](xref:fundamentals/logging/index) geschrieben werden. <xref:Microsoft.Extensions.Logging.ILogger> wird von App-Frameworks verwendet, um Protokolle zu schreiben, sowie von Benutzern für die eigene Protokollierung in einer App.
* **Ablaufverfolgung:** Ereignisse im Zusammenhang mit einem Vorgang werden mithilfe von `DiaganosticSource` und `Activity` geschrieben. Ablaufverfolgungen aus Diagnosequellen werden in der Regel dazu verwendet, um App-Telemetriedaten von Bibliotheken wie [Application Insights](/azure/azure-monitor/app/asp-net-core) und [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) zu sammeln.
* **Metriken:** Hierbei handelt es sich um eine Darstellung von Datenmesswerten über bestimmte Zeiträume hinweg, beispielsweise für Anforderungen pro Sekunde. Metriken werden mithilfe von `EventCounter` ausgegeben und können mithilfe des [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters)-Befehlszeilentools oder mit [Application Insights](/azure/azure-monitor/app/eventcounters) überwacht werden.

## <a name="logging"></a>Protokollierung

gRPC-Dienste und der gRPC-Client schreiben Protokolle mithilfe der [.NET Core-Protokollierung](xref:fundamentals/logging/index). Protokolle sind gut als Ausgangspunkt geeignet, wenn Sie ein unerwartetes Verhalten Ihrer Apps debuggen müssen.

### <a name="grpc-services-logging"></a>Protokollierung für gRPC-Dienste

> [!WARNING]
> Serverseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App. Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.

Da gRPC-Dienste bei ASP.NET Core gehostet werden, wird das ASP.NET Core-Protokollierungssystem verwendet. In der Standardkonfiguration werden für gRPC nur wenige Informationen protokolliert, das kann jedoch konfiguriert werden. In der Dokumentation zur [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index#configuration) finden Sie weitere Informationen zum Konfigurieren der ASP.NET Core-Protokollierung.

gRPC fügt Protokolle unter der `Grpc`-Kategorie hinzu. Wenn Sie in gRPC detaillierte Protokolle aktivieren möchten, konfigurieren Sie die `Grpc`-Präfixe für die `Debug`-Ebene in Ihrer Datei *appsettings.json* , indem Sie die folgenden Elemente im `LogLevel`-Unterbereich in `Logging` hinzufügen:

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

Sie können das auch in der Datei *Startup.cs* mit `ConfigureLogging` konfigurieren:

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie den folgenden Konfigurationswert in Ihrem Konfigurationssystem fest:

* `Logging:LogLevel:Grpc` = `Debug`

Suchen Sie in der Dokumentation nach Informationen zu Ihrem Konfigurationssystem, um zu bestimmen, wie geschachtelte Konfigurationswerte angegeben werden. Bei der Verwendung von Umgebungsvariablen werden beispielsweise zwei `_`-Zeichen anstelle von `:` verwendet (z. B. `Logging__LogLevel__Grpc`).

Es empfiehlt sich, die `Debug`-Ebene zu verwenden, wenn detailliertere Diagnosedaten Ihrer App gesammelt werden sollen. Die `Trace`-Ebene bietet nur sehr wenige Diagnoseinformationen und wird nur selten verwendet, um App-Probleme zu identifizieren.

#### <a name="sample-logging-output"></a>Beispiel einer Protokollierungsausgabe

Hier finden Sie ein Beispiel einer Konsolenausgabe auf der `Debug`-Ebene eines gRPC-Diensts:

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

### <a name="access-server-side-logs"></a>Zugreifen auf serverseitige Protokolle

Wie Sie auf serverseitige Protokolle zugreifen können, hängt von der Ausführungsumgebung ab.

#### <a name="as-a-console-app"></a>Ausführung als Konsolen-App

Wenn die Ausführung in einer Konsolen-App stattfindet, sollte die [Konsolenprotokollierung](xref:fundamentals/logging/index#console) standardmäßig aktiviert sein. gRPC-Protokolle werden in der Konsole angezeigt.

#### <a name="other-environments"></a>Andere Umgebungen

Wenn die App in einer anderen Umgebung bereitgestellt wird (z. B. Docker, Kubernetes oder Windows-Dienst) finden Sie unter <xref:fundamentals/logging/index> weitere Informationen zum Konfigurieren von Protokollierungsanbietern, die für die Umgebung geeignet sind.

### <a name="grpc-client-logging"></a>gRPC-Clientprotokollierung

> [!WARNING]
> Clientseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App. Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.

Wenn Sie Protokolle vom .NET-Client erhalten möchten, können Sie die `GrpcChannelOptions.LoggerFactory`-Eigenschaft festlegen, wenn der Kanal des Clients erstellt wird. Wenn Sie einen gRPC-Dienst in einer ASP.NET Core-App aufrufen, kann die Protokollierungsfactory aus einer Abhängigkeitsinjektion (Dependency Injection, DI) aufgelöst werden.

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

Eine alternative Möglichkeit, die Clientprotokollierung zu aktivieren, ist die Verwendung der [gRPC-Clientfactory](xref:grpc/clientfactory), um den Client zu erstellen. Ein für die Clientfactory registrierter und aus der DI aufgelöster gRPC-Client verwendet automatisch die für die App konfigurierte Protokollierung.

Wenn Ihre App die DI nicht verwendet, können Sie mit [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*) eine neue `ILoggerFactory`-Instanz erstellen. Fügen Sie Ihrer App das Paket [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) hinzu, um auf diese Methode zuzugreifen.

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>Protokollbereiche des gRPC-Clients

Der gRPC-Client fügt Protokollen, die während eines gRPC-Aufrufs erstellt werden, einen [Protokollierungsbereich](../fundamentals/logging/index.md#log-scopes) hinzu. Der Bereich verfügt über mit dem gRPC-Aufruf verbundene Metadaten:

* **GrpcMethodType:** Der gRPC-Methodentyp. Mögliche Werte sind Namen der `Grpc.Core.MethodType`-Enumeration, z. B. Unär.
* **GrpcUri:** Der relative URI der gRPC-Methode, z. B. /greet.Greeter/SayHellos.

#### <a name="sample-logging-output"></a>Beispiel einer Protokollierungsausgabe

Hier finden Sie ein Beispiel einer Konsolenausgabe auf der `Debug`-Ebene eines gRPC-Clients:

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

## <a name="tracing"></a>Ablaufverfolgung

gRPC-Dienste und der gRPC-Client stellen Informationen zu gRPC-Aufrufen mithilfe von [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) und [Activity](/dotnet/api/system.diagnostics.activity) zur Verfügung.

* .NET gRPC verwendet eine Activity-Klasse, um einen gRPC-Aufruf darzustellen.
* Ablaufverfolgungsereignisse werden bei Beginn und Ende der gRPC-Aufrufsaktivität in die Diagnosequelle geschrieben.
* Bei der Ablaufverfolgung werden keine Informationen dazu gesammelt, wann Nachrichten während der Lebensdauer eines gRPC-Streamingaufrufs gesendet werden.

### <a name="grpc-service-tracing"></a>gRPC-Dienstablaufsverfolgung

gRPC-Dienste werden bei ASP.NET Core gehostet, wo Ereignisse zu eingehenden HTTP-Anforderungen gemeldet werden. Für gRPC spezifische Metadaten werden den bestehenden HTTP-Anforderungsdiagnosedaten hinzugefügt, die von ASP.NET Core bereitgestellt werden.

* Der Name der Diagnosequelle ist `Microsoft.AspNetCore`.
* Der Name der Aktivität ist `Microsoft.AspNetCore.Hosting.HttpRequestIn`.
  * Der Name der gRPC-Methode, die vom gRPC-Aufruf aufgerufen wird, wird als Tag mit dem Namen `grpc.method` hinzugefügt.
  * Der Statuscode des gRPC-Aufrufs wird bei Abschluss als Tag mit dem Namen `grpc.status_code` hinzugefügt.

### <a name="grpc-client-tracing"></a>gPRC-Clientablaufverfolgung

Der .NET-gRPC-Client verwendet `HttpClient`, um gRPC-Aufrufe durchzuführen. Obwohl `HttpClient` Diagnoseereignisse schreibt, stellt der .NET-gRPC-Client eine benutzerdefinierte Diagnosequelle, eine Aktivität und Ereignisse zur Verfügung, sodass alle erforderlichen Informationen zu einem gRPC-Aufruf gesammelt werden können.

* Der Name der Diagnosequelle ist `Grpc.Net.Client`.
* Der Name der Aktivität ist `Grpc.Net.Client.GrpcOut`.
  * Der Name der gRPC-Methode, die vom gRPC-Aufruf aufgerufen wird, wird als Tag mit dem Namen `grpc.method` hinzugefügt.
  * Der Statuscode des gRPC-Aufrufs wird bei Abschluss als Tag mit dem Namen `grpc.status_code` hinzugefügt.

### <a name="collecting-tracing"></a>Sammeln von Ablaufverfolgungsdaten

Die einfachste Möglichkeit, `DiagnosticSource` zu verwenden, ist die Konfiguration einer Telemetriebibliothek wie [Application Insights](/azure/azure-monitor/app/asp-net-core) oder [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in Ihrer App. Die Bibliothek verarbeitet Informationen zu gRPC-Aufrufen zusammen mit weiteren App-Telemetriedaten.

Die Ablaufverfolgung kann in einem verwalteten Dienst wie Application Insights eingesehen werden, oder Sie entscheiden sich dazu, Ihr eigenes verteiltes Ablaufverfolgungssystem auszuführen. OpenTelemetry unterstützt das Exportieren von Ablaufverfolgungsdaten zu [Jaeger](https://www.jaegertracing.io/) und [Zipkin](https://zipkin.io/).

`DiagnosticSource` kann Ablaufverfolgungsereignisse im Code mithilfe von `DiagnosticListener` verarbeiten. Weitere Informationen zum Lauschen auf einer Diagnosequelle mit Code finden Sie unter [Verarbeiten von Daten mithilfe von DiagnosticListener](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).

> [!NOTE]
> Telemetriebibliotheken erfassen aktuell keine gRPC-spezifischen `Grpc.Net.Client.GrpcOut`-Telemetriedaten. Aktuell wird aber daran gearbeitet, die Telemetriebibliotheken zu optimieren, sodass diese Ablaufverfolgung erfasst wird.

## <a name="metrics"></a>Metriken

Bei Metriken handelt es sich um eine Darstellung von Datenmesswerten über bestimmte Zeiträume hinweg, beispielsweise für Anforderungen pro Sekunde. Metrikdaten ermöglichen die Überwachung des Zustands einer App auf einer hohen Ebene. .NET-gRPC-Metriken werden mithilfe von `EventCounter` ausgegeben.

### <a name="grpc-service-metrics"></a>gRPC-Dienstmetriken

gRPC-Servermetriken werden in der `Grpc.AspNetCore.Server`-Ereignisquelle gemeldet.

| name                      | Beschreibung                   |
| --------------------------|-------------------------------|
| `total-calls`             | Aufrufe gesamt                   |
| `current-calls`           | Aktuelle Aufrufe                 |
| `calls-failed`            | Insgesamt fehlgeschlagene Aufrufe            |
| `calls-deadline-exceeded` | Anzahl an Aufrufen, die eine Frist überschritten haben |
| `messages-sent`           | Gesamtzahl der gesendeten Nachrichten           |
| `messages-received`       | Insgesamt empfangene Nachrichten       |
| `calls-unimplemented`     | Insgesamt nicht implementierte Aufrufe     |

In ASP.NET Core stehen auch eigene Metriken für die `Microsoft.AspNetCore.Hosting`-Ereignisquelle zur Verfügung.

### <a name="grpc-client-metrics"></a>gRPC-Clientmetriken

gRPC-Clientmetriken werden in der `Grpc.Net.Client`-Ereignisquelle gemeldet.

| name                      | Beschreibung                   |
| --------------------------|-------------------------------|
| `total-calls`             | Aufrufe gesamt                   |
| `current-calls`           | Aktuelle Aufrufe                 |
| `calls-failed`            | Insgesamt fehlgeschlagene Aufrufe            |
| `calls-deadline-exceeded` | Anzahl an Aufrufen, die eine Frist überschritten haben |
| `messages-sent`           | Gesamtzahl der gesendeten Nachrichten           |
| `messages-received`       | Insgesamt empfangene Nachrichten       |

### <a name="observe-metrics"></a>Überwachen von Metriken

[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) ist ein Leistungsüberwachungstool zur Ad-hoc-Überwachung der Integrität und zur Leistungsuntersuchung auf erster Ebene. Sie können eine .NET-App entweder mit `Grpc.AspNetCore.Server` oder `Grpc.Net.Client` als Anbietername überwachen.

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

Eine andere Möglichkeit, gRPC-Metriken zu überwachen, ist es, Indikatoren mithilfe des [Microsoft.ApplicationInsights.EventCounterCollector-Pakets](/azure/azure-monitor/app/eventcounters) von Application Insights zu sammeln. Sobald Application Insights eingerichtet ist, werden allgemeine .NET-Indikatoren zur Laufzeit gesammelt. Indikatoren von gRPC werden nicht standardmäßig gesammelt, aber Application Insights kann so [angepasst werden, dass zusätzliche Indikatoren eingeschlossen](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected) werden.

Geben Sie die gRPC-Indikatoren, die gesammelt werden sollen, für Application Insights in *Startup.cs* :

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>