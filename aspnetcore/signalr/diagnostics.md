---
title: 'Protokollierung und Diagnose in ASP.net Core SignalR'
author: anurse
description: 'Erfahren Sie, wie Sie Diagnoseinformationen aus Ihrer ASP.net Core- SignalR App erfassen.'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr, devx-track-js
ms.date: 06/12/2020
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
uid: signalr/diagnostics
ms.openlocfilehash: 6e5e9d866a1e03e69856cc63dcfe30284048dd6d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061313"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="964ac-103">Protokollierung und Diagnose in ASP.net Core SignalR</span><span class="sxs-lookup"><span data-stu-id="964ac-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="964ac-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="964ac-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="964ac-105">Dieser Artikel enthält Anleitungen zum Sammeln von Diagnoseinformationen aus Ihrer ASP.net Core- SignalR app, um Probleme bei der Problembehandlung zu beheben.</span><span class="sxs-lookup"><span data-stu-id="964ac-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="964ac-106">Server seitige Protokollierung</span><span class="sxs-lookup"><span data-stu-id="964ac-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="964ac-107">Serverseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="964ac-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="964ac-108">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="964ac-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="964ac-109">Da SignalR Teil ASP.net Core ist, wird das ASP.net Core Protokollierungs System verwendet.</span><span class="sxs-lookup"><span data-stu-id="964ac-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="964ac-110">In der Standardkonfiguration protokolliert nur wenige SignalR Informationen, aber dies kann konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="964ac-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="964ac-111">In der Dokumentation zur [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index#configuration) finden Sie weitere Informationen zum Konfigurieren der ASP.NET Core-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="964ac-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="964ac-112">SignalR verwendet zwei Kategorien von Kategorien:</span><span class="sxs-lookup"><span data-stu-id="964ac-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="964ac-113">`Microsoft.AspNetCore.SignalR`: Bei Protokollen im Zusammenhang mit hubprotokollen, beim Aktivieren von Hubs, beim Aufrufen von Methoden und anderen hubbezogenen Aktivitäten.</span><span class="sxs-lookup"><span data-stu-id="964ac-113">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="964ac-114">`Microsoft.AspNetCore.Http.Connections`: Bei Protokollen im Zusammenhang mit Transporten, z. b. websockets, langem Abruf, Server-Sent Ereignissen und Low-Level- SignalR Infrastruktur.</span><span class="sxs-lookup"><span data-stu-id="964ac-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="964ac-115">Um ausführliche Protokolle von zu aktivieren SignalR , konfigurieren Sie beide vorangehenden Präfixe für die `Debug` Ebene in der Datei, *appsettings.json* indem Sie dem `LogLevel` unter Abschnitt in die folgenden Elemente hinzufügen `Logging` :</span><span class="sxs-lookup"><span data-stu-id="964ac-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="964ac-116">Sie können dies auch in Code in der- `CreateWebHostBuilder` Methode konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="964ac-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="964ac-117">Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie die folgenden Konfigurationswerte in Ihrem Konfigurationssystem fest:</span><span class="sxs-lookup"><span data-stu-id="964ac-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="964ac-118">Suchen Sie in der Dokumentation nach Informationen zu Ihrem Konfigurationssystem, um zu bestimmen, wie geschachtelte Konfigurationswerte angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="964ac-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="964ac-119">Bei der Verwendung von Umgebungsvariablen werden beispielsweise zwei `_`-Zeichen anstelle von `:` verwendet (z. B. `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="964ac-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="964ac-120">Es empfiehlt sich, die `Debug`-Ebene zu verwenden, wenn detailliertere Diagnosedaten Ihrer App gesammelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="964ac-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="964ac-121">Die `Trace`-Ebene bietet nur sehr wenige Diagnoseinformationen und wird nur selten verwendet, um App-Probleme zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="964ac-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="964ac-122">Zugreifen auf serverseitige Protokolle</span><span class="sxs-lookup"><span data-stu-id="964ac-122">Access server-side logs</span></span>

<span data-ttu-id="964ac-123">Wie Sie auf serverseitige Protokolle zugreifen können, hängt von der Ausführungsumgebung ab.</span><span class="sxs-lookup"><span data-stu-id="964ac-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="964ac-124">Als Konsolen-App außerhalb von IIS</span><span class="sxs-lookup"><span data-stu-id="964ac-124">As a console app outside IIS</span></span>

<span data-ttu-id="964ac-125">Wenn die Ausführung in einer Konsolen-App stattfindet, sollte die [Konsolenprotokollierung](xref:fundamentals/logging/index#console) standardmäßig aktiviert sein.</span><span class="sxs-lookup"><span data-stu-id="964ac-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="964ac-126">SignalR die Protokolle werden in der-Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="964ac-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="964ac-127">In IIS Express aus Visual Studio</span><span class="sxs-lookup"><span data-stu-id="964ac-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="964ac-128">Visual Studio zeigt die Protokoll Ausgabe im **Ausgabe** Fenster an.</span><span class="sxs-lookup"><span data-stu-id="964ac-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="964ac-129">Wählen Sie die Dropdown Option **ASP.net Core Webserver** aus.</span><span class="sxs-lookup"><span data-stu-id="964ac-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="964ac-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="964ac-130">Azure App Service</span></span>

<span data-ttu-id="964ac-131">Aktivieren Sie die Option **Anwendungs Protokollierung (File System)** im Abschnitt **Diagnoseprotokolle** des Azure App Service Portals, und konfigurieren Sie die **Ebene** auf `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="964ac-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="964ac-132">Protokolle sollten über den **protokollstreamingdienst** und in Protokollen im Dateisystem des App Service verfügbar sein.</span><span class="sxs-lookup"><span data-stu-id="964ac-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="964ac-133">Weitere Informationen finden Sie unter [Azure-Protokoll Streaming](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="964ac-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="964ac-134">Andere Umgebungen</span><span class="sxs-lookup"><span data-stu-id="964ac-134">Other environments</span></span>

<span data-ttu-id="964ac-135">Wenn die App in einer anderen Umgebung bereitgestellt wird (z. B. Docker, Kubernetes oder Windows-Dienst) finden Sie unter <xref:fundamentals/logging/index> weitere Informationen zum Konfigurieren von Protokollierungsanbietern, die für die Umgebung geeignet sind.</span><span class="sxs-lookup"><span data-stu-id="964ac-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="964ac-136">JavaScript-Client Protokollierung</span><span class="sxs-lookup"><span data-stu-id="964ac-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="964ac-137">Clientseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="964ac-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="964ac-138">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="964ac-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="964ac-139">Wenn Sie den JavaScript-Client verwenden, können Sie die Protokollierungs Optionen mithilfe der `configureLogging` Methode für konfigurieren `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="964ac-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="964ac-140">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="964ac-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="964ac-141">In der folgenden Tabelle werden die für den JavaScript-Client verfügbaren Protokoll Ebenen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="964ac-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="964ac-142">Wenn Sie die Protokollebene auf einen dieser Werte festlegen, wird die Protokollierung auf dieser Ebene und allen darüber liegenden Ebenen in der Tabelle ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="964ac-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="964ac-143">Ebene</span><span class="sxs-lookup"><span data-stu-id="964ac-143">Level</span></span> | <span data-ttu-id="964ac-144">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="964ac-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="964ac-145">Es werden keine Nachrichten protokolliert.</span><span class="sxs-lookup"><span data-stu-id="964ac-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="964ac-146">Meldungen, die auf einen Fehler in der gesamten App hindeuten.</span><span class="sxs-lookup"><span data-stu-id="964ac-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="964ac-147">Meldungen, die auf einen Fehler im aktuellen Vorgang hindeuten.</span><span class="sxs-lookup"><span data-stu-id="964ac-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="964ac-148">Meldungen, die auf ein nicht schwer wiedliches Problem hinweisen.</span><span class="sxs-lookup"><span data-stu-id="964ac-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="964ac-149">Informationsmeldungen.</span><span class="sxs-lookup"><span data-stu-id="964ac-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="964ac-150">Diagnosemeldungen sind für das Debuggen nützlich.</span><span class="sxs-lookup"><span data-stu-id="964ac-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="964ac-151">Sehr ausführliche Diagnosemeldungen, die für die Diagnose bestimmter Probleme entwickelt wurden.</span><span class="sxs-lookup"><span data-stu-id="964ac-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="964ac-152">Nachdem Sie die Ausführlichkeit konfiguriert haben, werden die Protokolle in die Browser Konsole (oder die Standard Ausgabe in einer nodejs-APP) geschrieben.</span><span class="sxs-lookup"><span data-stu-id="964ac-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="964ac-153">Wenn Sie Protokolle an ein benutzerdefiniertes Protokollierungs System senden möchten, können Sie ein JavaScript-Objekt bereitstellen, das die- `ILogger` Schnittstelle implementiert.</span><span class="sxs-lookup"><span data-stu-id="964ac-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="964ac-154">Die einzige Methode, die implementiert werden muss `log` , ist, die die Ebene des Ereignisses und die dem Ereignis zugeordnete Meldung annimmt.</span><span class="sxs-lookup"><span data-stu-id="964ac-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="964ac-155">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="964ac-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="964ac-156"> .NET-Clientprotokollierung</span><span class="sxs-lookup"><span data-stu-id="964ac-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="964ac-157">Clientseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="964ac-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="964ac-158">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="964ac-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="964ac-159">Um Protokolle vom .NET-Client zu erhalten, können Sie die- `ConfigureLogging` Methode für verwenden `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="964ac-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="964ac-160">Dies funktioniert genauso wie die `ConfigureLogging` -Methode in `WebHostBuilder` und `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="964ac-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="964ac-161">Sie können die gleichen Protokollierungs Anbieter konfigurieren, die Sie in ASP.net Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="964ac-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="964ac-162">Allerdings müssen Sie die nuget-Pakete für die einzelnen Protokollierungs Anbieter manuell installieren und aktivieren.</span><span class="sxs-lookup"><span data-stu-id="964ac-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="964ac-163">Weitere Informationen zum Hinzufügen der .NET-Client Protokollierung zu einer- Blazor WebAssembly App <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging></span><span class="sxs-lookup"><span data-stu-id="964ac-163">To add .NET client logging to a Blazor WebAssembly app, see <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="964ac-164">Konsolenprotokollierung</span><span class="sxs-lookup"><span data-stu-id="964ac-164">Console logging</span></span>

<span data-ttu-id="964ac-165">Fügen Sie das Paket [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) hinzu, um die Konsolen Protokollierung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="964ac-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="964ac-166">Verwenden Sie dann die- `AddConsole` Methode zum Konfigurieren der Konsolen Protokollierung:</span><span class="sxs-lookup"><span data-stu-id="964ac-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="964ac-167">Debuggen des Ausgabe Fensters</span><span class="sxs-lookup"><span data-stu-id="964ac-167">Debug output window logging</span></span>

<span data-ttu-id="964ac-168">Sie können Protokolle auch so konfigurieren, dass Sie zum Fenster **Ausgabe** in Visual Studio wechseln.</span><span class="sxs-lookup"><span data-stu-id="964ac-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="964ac-169">Installieren Sie das Paket " [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) ", und verwenden Sie die- `AddDebug` Methode:</span><span class="sxs-lookup"><span data-stu-id="964ac-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="964ac-170">Andere Protokollierungs Anbieter</span><span class="sxs-lookup"><span data-stu-id="964ac-170">Other logging providers</span></span>

<span data-ttu-id="964ac-171">SignalR unterstützt andere Protokollierungs Anbieter wie serilog, seq, nlog oder ein beliebiges anderes Protokollierungs System, das in integriert ist `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="964ac-171">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="964ac-172">Wenn Ihr Protokollierungs System ein bereitstellt `ILoggerProvider` , können Sie es mit folgenden Aktionen registrieren `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="964ac-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="964ac-173">Ausführlichkeit von Steuerelementen</span><span class="sxs-lookup"><span data-stu-id="964ac-173">Control verbosity</span></span>

<span data-ttu-id="964ac-174">Wenn Sie sich an anderen Stellen in ihrer App anmelden, ist das Ändern der Standard Ebene in `Debug` möglicherweise zu ausführlich.</span><span class="sxs-lookup"><span data-stu-id="964ac-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="964ac-175">Sie können einen Filter verwenden, um den Protokolliergrad für Protokolle zu konfigurieren SignalR .</span><span class="sxs-lookup"><span data-stu-id="964ac-175">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="964ac-176">Dies kann im Code auf die gleiche Weise wie auf dem Server erfolgen:</span><span class="sxs-lookup"><span data-stu-id="964ac-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="964ac-177">Netzwerkablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="964ac-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="964ac-178">Eine Netzwerk Ablauf Verfolgung enthält den gesamten Inhalt jeder Nachricht, die von Ihrer APP gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="964ac-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="964ac-179">Veröffentlichen Sie **niemals** unformatierte Netzwerk Ablauf Verfolgungen aus Produktions-apps in öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="964ac-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="964ac-180">Wenn ein Problem auftritt, kann es vorkommen, dass eine Netzwerk Ablauf Verfolgung viele hilfreiche Informationen liefert.</span><span class="sxs-lookup"><span data-stu-id="964ac-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="964ac-181">Dies ist besonders nützlich, wenn Sie ein Problem in unserer Problemverfolgung melden.</span><span class="sxs-lookup"><span data-stu-id="964ac-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="964ac-182">Erfassen einer Netzwerk Ablauf Verfolgung mit "fddler" (bevorzugte Option)</span><span class="sxs-lookup"><span data-stu-id="964ac-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="964ac-183">Diese Methode funktioniert für alle apps.</span><span class="sxs-lookup"><span data-stu-id="964ac-183">This method works for all apps.</span></span>

<span data-ttu-id="964ac-184">"Fddler" ist ein sehr leistungsfähiges Tool zum Erfassen von http-Ablauf Verfolgungen.</span><span class="sxs-lookup"><span data-stu-id="964ac-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="964ac-185">Installieren Sie Sie über [Telerik.com/Fiddler](https://www.telerik.com/fiddler), starten Sie Sie, und führen Sie dann die APP aus.</span><span class="sxs-lookup"><span data-stu-id="964ac-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="964ac-186">"Fddler" ist für Windows verfügbar, und es gibt Beta Versionen für macOS und Linux.</span><span class="sxs-lookup"><span data-stu-id="964ac-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="964ac-187">Wenn Sie eine Verbindung über HTTPS herstellen, sind einige zusätzliche Schritte erforderlich, um sicherzustellen, dass der HTTPS-Datenverkehr von fddler entschlüsselt werden kann</span><span class="sxs-lookup"><span data-stu-id="964ac-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="964ac-188">Weitere Informationen finden Sie in der [Dokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)zu "Dokumentation".</span><span class="sxs-lookup"><span data-stu-id="964ac-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="964ac-189">Nachdem Sie die Ablauf Verfolgung gesammelt haben, können Sie die Ablauf Verfolgung exportieren **File** , indem Sie auf  >  **Save**  >  der Menüleiste auf Datei **alle Sitzungen** speichern klicken.</span><span class="sxs-lookup"><span data-stu-id="964ac-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportieren aller Sitzungen aus "fddler"](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="964ac-191">Erfassen einer Netzwerk Ablauf Verfolgung mit tcpdump (nur macOS und Linux)</span><span class="sxs-lookup"><span data-stu-id="964ac-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="964ac-192">Diese Methode funktioniert für alle apps.</span><span class="sxs-lookup"><span data-stu-id="964ac-192">This method works for all apps.</span></span>

<span data-ttu-id="964ac-193">Sie können unformatierte TCP-Ablauf Verfolgungen mithilfe von tcpdump erfassen, indem Sie den folgenden Befehl in einer Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="964ac-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="964ac-194">`root` `sudo` Wenn Sie einen Berechtigungs Fehler erhalten, müssen Sie möglicherweise den Befehl mit einem Präfix versehen.</span><span class="sxs-lookup"><span data-stu-id="964ac-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="964ac-195">Ersetzen `[interface]` Sie durch die Netzwerkschnittstelle, die Sie erfassen möchten.</span><span class="sxs-lookup"><span data-stu-id="964ac-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="964ac-196">In der Regel handelt es sich hierbei um `/dev/eth0` (für die standardethernet-Schnittstelle) oder `/dev/lo0` (für localhost-Datenverkehr).</span><span class="sxs-lookup"><span data-stu-id="964ac-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="964ac-197">Weitere Informationen finden `tcpdump` Sie auf der Seite "man" auf Ihrem Host System.</span><span class="sxs-lookup"><span data-stu-id="964ac-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="964ac-198">Erfassen einer Netzwerk Ablauf Verfolgung im Browser</span><span class="sxs-lookup"><span data-stu-id="964ac-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="964ac-199">Diese Methode funktioniert nur bei browserbasierten apps.</span><span class="sxs-lookup"><span data-stu-id="964ac-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="964ac-200">Die meisten Browser Entwicklertools über eine Registerkarte "Netzwerk" verfügen, mit der Sie die Netzwerkaktivität zwischen dem Browser und dem Server erfassen können.</span><span class="sxs-lookup"><span data-stu-id="964ac-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="964ac-201">Diese Ablauf Verfolgungen enthalten jedoch keine WebSocket-und Server-Sent Ereignis Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="964ac-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="964ac-202">Wenn Sie diese Transporte verwenden, ist die Verwendung eines Tools wie z. b. "fddler" oder "tcpdump" (unten beschrieben) ein besserer Ansatz.</span><span class="sxs-lookup"><span data-stu-id="964ac-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="964ac-203">Microsoft Edge und Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="964ac-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="964ac-204">(Die Anweisungen sind für Edge und Internet Explorer identisch.)</span><span class="sxs-lookup"><span data-stu-id="964ac-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="964ac-205">Drücken Sie F12, um die Entwickler Tools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="964ac-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="964ac-206">Klicken Sie auf die Registerkarte Netzwerk</span><span class="sxs-lookup"><span data-stu-id="964ac-206">Click the Network Tab</span></span>
3. <span data-ttu-id="964ac-207">Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.</span><span class="sxs-lookup"><span data-stu-id="964ac-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="964ac-208">Klicken Sie in der Symbolleiste auf das Symbol speichern, um die Ablauf Verfolgung als "har"-Datei zu exportieren:</span><span class="sxs-lookup"><span data-stu-id="964ac-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Symbol "Speichern" auf der Registerkarte "Netzwerk" von Microsoft Edge dev tools](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="964ac-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="964ac-210">Google Chrome</span></span>

1. <span data-ttu-id="964ac-211">Drücken Sie F12, um die Entwickler Tools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="964ac-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="964ac-212">Klicken Sie auf die Registerkarte Netzwerk</span><span class="sxs-lookup"><span data-stu-id="964ac-212">Click the Network Tab</span></span>
3. <span data-ttu-id="964ac-213">Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.</span><span class="sxs-lookup"><span data-stu-id="964ac-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="964ac-214">Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Anforderungsliste, und wählen Sie "Speichern unter har mit Inhalt"</span><span class="sxs-lookup"><span data-stu-id="964ac-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Option "Speichern unter har mit Inhalt" auf der Registerkarte "Google Chrome dev Tools Network"](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="964ac-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="964ac-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="964ac-217">Drücken Sie F12, um die Entwickler Tools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="964ac-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="964ac-218">Klicken Sie auf die Registerkarte Netzwerk</span><span class="sxs-lookup"><span data-stu-id="964ac-218">Click the Network Tab</span></span>
3. <span data-ttu-id="964ac-219">Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.</span><span class="sxs-lookup"><span data-stu-id="964ac-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="964ac-220">Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Liste der Anforderungen, und wählen Sie "alles als har</span><span class="sxs-lookup"><span data-stu-id="964ac-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Option "alle als har speichern" auf der Registerkarte "Mozilla Firefox dev Tools Network"](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="964ac-222">Anfügen von Diagnose Dateien an GitHub-Probleme</span><span class="sxs-lookup"><span data-stu-id="964ac-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="964ac-223">Sie können Diagnose Dateien an GitHub-Probleme anfügen, indem Sie Sie so umbenennen, dass Sie über eine Erweiterung verfügen, und Sie `.txt` dann auf das Problem ziehen und ablegen.</span><span class="sxs-lookup"><span data-stu-id="964ac-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="964ac-224">Fügen Sie den Inhalt von Protokolldateien oder Netzwerk Ablauf Verfolgungen nicht in ein GitHub-Problem ein.</span><span class="sxs-lookup"><span data-stu-id="964ac-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="964ac-225">Diese Protokolle und Ablauf Verfolgungen können sehr umfangreich sein, und GitHub verkürzt Sie in der Regel.</span><span class="sxs-lookup"><span data-stu-id="964ac-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Ziehen von Protokolldateien auf ein GitHub-Problem](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="964ac-227">Metriken</span><span class="sxs-lookup"><span data-stu-id="964ac-227">Metrics</span></span>

<span data-ttu-id="964ac-228">Metriken sind eine Darstellung von Daten Messungen in Zeitintervallen.</span><span class="sxs-lookup"><span data-stu-id="964ac-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="964ac-229">Beispielsweise Anforderungen pro Sekunde.</span><span class="sxs-lookup"><span data-stu-id="964ac-229">For example, requests per second.</span></span> <span data-ttu-id="964ac-230">Metrikdaten ermöglichen eine Überwachung des Zustands einer APP auf hoher Ebene.</span><span class="sxs-lookup"><span data-stu-id="964ac-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="964ac-231">.NET-gRPC-Metriken werden mithilfe von <xref:System.Diagnostics.Tracing.EventCounter> ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="964ac-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="no-locsignalr-server-metrics"></a><span data-ttu-id="964ac-232">SignalR servermetriken</span><span class="sxs-lookup"><span data-stu-id="964ac-232">SignalR server metrics</span></span>

<span data-ttu-id="964ac-233">SignalR servermetriken werden auf der <xref:Microsoft.AspNetCore.Http.Connections> Ereignis Quelle gemeldet.</span><span class="sxs-lookup"><span data-stu-id="964ac-233">SignalR server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="964ac-234">Name</span><span class="sxs-lookup"><span data-stu-id="964ac-234">Name</span></span>                    | <span data-ttu-id="964ac-235">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="964ac-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="964ac-236">Gestartete Verbindungen gesamt</span><span class="sxs-lookup"><span data-stu-id="964ac-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="964ac-237">Beendete Verbindungen insgesamt</span><span class="sxs-lookup"><span data-stu-id="964ac-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="964ac-238">Timeout bei Verbindungen gesamt</span><span class="sxs-lookup"><span data-stu-id="964ac-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="964ac-239">Aktuelle Verbindungen</span><span class="sxs-lookup"><span data-stu-id="964ac-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="964ac-240">Durchschnittliche Verbindungs Dauer</span><span class="sxs-lookup"><span data-stu-id="964ac-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="964ac-241">Überwachen von Metriken</span><span class="sxs-lookup"><span data-stu-id="964ac-241">Observe metrics</span></span>

<span data-ttu-id="964ac-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) ist ein Leistungsüberwachungstool zur Ad-hoc-Überwachung der Integrität und zur Leistungsuntersuchung auf erster Ebene.</span><span class="sxs-lookup"><span data-stu-id="964ac-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="964ac-243">Überwachen Sie eine .net-App mit `Microsoft.AspNetCore.Http.Connections` als Anbieter Namen.</span><span class="sxs-lookup"><span data-stu-id="964ac-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="964ac-244">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="964ac-244">For example:</span></span>

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a><span data-ttu-id="964ac-245">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="964ac-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
