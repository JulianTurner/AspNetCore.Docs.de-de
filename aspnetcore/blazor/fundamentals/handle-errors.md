---
title: Fehlerbehandlung in ASP.NET Core Blazor-Apps
author: guardrex
description: Erfahren Sie, wie ASP.NET Core Blazor wie Blazor Ausnahmefehler behandelt und wie Sie Apps entwickeln können, die Fehler erkennen und behandeln.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
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
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: 5a255c2d3535311cecd6b7219447e80d1ae78877
ms.sourcegitcommit: d4836f9b7c508f51c6c4ee6d0cc719b38c1729c4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98758246"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a><span data-ttu-id="1fee9-103">Fehlerbehandlung in ASP.NET Core Blazor-Apps</span><span class="sxs-lookup"><span data-stu-id="1fee9-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="1fee9-104">Von [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="1fee9-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="1fee9-105">In diesem Artikel wird beschrieben, wie Blazor Ausnahmefehler behandelt und wie Sie Apps entwickeln können, die Fehler erkennen und behandeln.</span><span class="sxs-lookup"><span data-stu-id="1fee9-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="1fee9-106">Ausführliche Fehler bei der Entwicklung</span><span class="sxs-lookup"><span data-stu-id="1fee9-106">Detailed errors during development</span></span>

<span data-ttu-id="1fee9-107">Wenn eine Blazor-App während der Entwicklung nicht ordnungsgemäß funktioniert, erhalten Sie nun ausführliche Fehlerinformationen von der App, die Sie beim Beheben des Problems unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="1fee9-108">Wenn ein Fehler auftritt, zeigen Blazor-Apps eine goldene Leiste am unteren Rand der Anzeige an:</span><span class="sxs-lookup"><span data-stu-id="1fee9-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="1fee9-109">Während der Entwicklung leitet die goldene Leiste Sie an die Browserkonsole weiter, in der die Ausnahme angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="1fee9-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="1fee9-110">In der Produktion benachrichtigt die goldene Leiste den Benutzer darüber, dass ein Fehler aufgetreten ist, und empfiehlt eine Aktualisierung des Browsers.</span><span class="sxs-lookup"><span data-stu-id="1fee9-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="1fee9-111">Die Benutzeroberfläche für diese Fehlerbehandlung ist Teil der Blazor-Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="1fee9-112">Passen Sie in einer Blazor WebAssembly-App die Darstellung in der `wwwroot/index.html`-Datei an:</span><span class="sxs-lookup"><span data-stu-id="1fee9-112">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="1fee9-113">Passen Sie in einer Blazor Server-App die Darstellung in der `Pages/_Host.cshtml`-Datei an:</span><span class="sxs-lookup"><span data-stu-id="1fee9-113">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="1fee9-114">Das `blazor-error-ui`-Element wird von den Stilen in den Blazor-Vorlagen (`wwwroot/css/app.css` oder `wwwroot/css/site.css`) verborgen und nur angezeigt, wenn ein Fehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="1fee9-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="blazor-server-detailed-circuit-errors"></a><span data-ttu-id="1fee9-115">Detaillierte Blazor Server-Leitungsfehler</span><span class="sxs-lookup"><span data-stu-id="1fee9-115">Blazor Server detailed circuit errors</span></span>

<span data-ttu-id="1fee9-116">Clientseitige Fehlermeldungen enthalten weder den Aufrufstapel noch Details zur Fehlerursache, Serverprotokolle hingegen schon.</span><span class="sxs-lookup"><span data-stu-id="1fee9-116">Client-side errors don't include the callstack and don't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="1fee9-117">Zu Entwicklungszwecken können vertrauliche Informationen zu Leitungsfehlern für den Client verfügbar gemacht werden, indem Sie detaillierte Fehlermeldungen aktivieren.</span><span class="sxs-lookup"><span data-stu-id="1fee9-117">For development purposes, sensitive circuit error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="1fee9-118">Aktivieren Sie detaillierte Blazor Server-Fehler mithilfe der folgenden Verfahren:</span><span class="sxs-lookup"><span data-stu-id="1fee9-118">Enable Blazor Server detailed errors using the following approaches:</span></span>

* <span data-ttu-id="1fee9-119"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="1fee9-119"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="1fee9-120">Legen Sie den Konfigurationsschlüssel `DetailedErrors` auf `true` fest. Diese Festlegung kann in der Datei mit den Entwicklungseinstellungen der App (`appsettings.Development.json`) erfolgen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-120">The `DetailedErrors` configuration key set to `true`, which can be set in the app's Development settings file (`appsettings.Development.json`).</span></span> <span data-ttu-id="1fee9-121">Der Schlüssel lässt sich darüber hinaus mithilfe der Umgebungsvariablen `ASPNETCORE_DETAILEDERRORS` auf den Wert `true` festlegen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-121">The key can also be set using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true`.</span></span>
* <span data-ttu-id="1fee9-122">Die [serverseitige Protokollierung von SignalR](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) kann auf [Debuggen](xref:Microsoft.Extensions.Logging.LogLevel) oder [Ablaufverfolgung](xref:Microsoft.Extensions.Logging.LogLevel) zur detaillierten Protokollierung von SignalR festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="1fee9-122">[SignalR server-side logging](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) can be set to [Debug](xref:Microsoft.Extensions.Logging.LogLevel) or [Trace](xref:Microsoft.Extensions.Logging.LogLevel) for detailed SignalR logging.</span></span>

<span data-ttu-id="1fee9-123">`appsettings.Development.json`:</span><span class="sxs-lookup"><span data-stu-id="1fee9-123">`appsettings.Development.json`:</span></span>

```json
{
  "DetailedErrors": true,
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information",
      "Microsoft.AspNetCore.SignalR": "Debug"
    }
  }
}
```

> [!WARNING]
> <span data-ttu-id="1fee9-124">Das Verfügbarmachen von Fehlerinformationen für internetseitige Clients ist ein Sicherheitsrisiko, dass immer vermieden werden sollte.</span><span class="sxs-lookup"><span data-stu-id="1fee9-124">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="1fee9-125">Reaktion einer Blazor Server-App auf Ausnahmefehler</span><span class="sxs-lookup"><span data-stu-id="1fee9-125">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="1fee9-126">Blazor Server ist ein zustandsbehaftetes Framework.</span><span class="sxs-lookup"><span data-stu-id="1fee9-126">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="1fee9-127">Während Benutzer mit einer App interagieren, besteht eine Verbindung mit dem Server, die als *Leitung* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="1fee9-127">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="1fee9-128">Die Leitung enthält aktive Instanzen von Komponenten und weist zahlreiche andere Aspekte zum Zustand auf wie:</span><span class="sxs-lookup"><span data-stu-id="1fee9-128">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="1fee9-129">Die zuletzt gerenderte Ausgabe von Komponenten</span><span class="sxs-lookup"><span data-stu-id="1fee9-129">The most recent rendered output of components.</span></span>
* <span data-ttu-id="1fee9-130">Die aktuellen Delegate zur Ereignisbehandlung, die durch clientseitige Ereignisse ausgelöst werden können</span><span class="sxs-lookup"><span data-stu-id="1fee9-130">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="1fee9-131">Wenn ein Benutzer die App in mehreren Registerkarten eines Browsers öffnet, bestehen mehrere unabhängige Leitungen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-131">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="1fee9-132">Die meisten Ausnahmefehler werden von Blazor als für die Leitung schwerwiegende Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-132">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="1fee9-133">Wenn eine Leitung aufgrund eines Ausnahmefehlers beendet wird, kann der Benutzer nur dann mit der App weiter interagieren, wenn die Seite nochmals geladen und so eine neue Leitung erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="1fee9-133">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="1fee9-134">Leitungen außerhalb der beendeten Leitung, bei denen es sich um Leitungen für andere Benutzer oder andere Browserregisterkarten handelt, sind davon nicht betroffen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-134">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="1fee9-135">Dieses Szenario ähnelt einer Desktop-App, die abstürzt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-135">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="1fee9-136">Die abgestürzte App muss neu gestartet werden, aber andere Apps sind nicht betroffen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-136">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="1fee9-137">Eine Leitung wird beendet, wenn aus folgenden Gründen ein Ausnahmefehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="1fee9-137">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="1fee9-138">Bei einem Ausnahmefehler wird die Leitung häufig in einen nicht definierten Zustand versetzt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-138">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="1fee9-139">Nach einem Ausnahmefehler kann ein normales Funktionieren der App nicht mehr sichergestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1fee9-139">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="1fee9-140">Wenn die Leitung weiter bestehen bleibt, können für die App Sicherheitsrisiken bestehen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-140">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="1fee9-141">Behandeln von Ausnahmefehlern in Entwicklercode</span><span class="sxs-lookup"><span data-stu-id="1fee9-141">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="1fee9-142">Damit die Ausführung einer App nach einem Fehler fortgesetzt werden kann, muss die App eine Fehlerbehandlungslogik enthalten.</span><span class="sxs-lookup"><span data-stu-id="1fee9-142">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="1fee9-143">In späteren Abschnitten dieses Artikels werden mögliche Quellen für Ausnahmefehler beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1fee9-143">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="1fee9-144">Rendern Sie in einer Produktionsumgebung keine Ausnahmemeldungen des Frameworks oder Stapelüberwachungen in der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="1fee9-144">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="1fee9-145">Durch Rendern von Ausnahmemeldungen oder Stapelüberwachungen kann Folgendes geschehen:</span><span class="sxs-lookup"><span data-stu-id="1fee9-145">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="1fee9-146">Vertrauliche Informationen werden gegenüber Endbenutzern offengelegt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-146">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="1fee9-147">Böswilligen Benutzern wird geholfen, Schwachstellen in einer App zu erkennen, die die Sicherheit von Server, App oder Netzwerk beeinträchtigen können.</span><span class="sxs-lookup"><span data-stu-id="1fee9-147">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="1fee9-148">Protokollieren von Fehlern bei einem permanenten Anbieter</span><span class="sxs-lookup"><span data-stu-id="1fee9-148">Log errors with a persistent provider</span></span>

<span data-ttu-id="1fee9-149">Im Fall eines Ausnahmefehlers wird die Ausnahme in den im Dienstcontainer konfigurierten<xref:Microsoft.Extensions.Logging.ILogger>-Instanzen protokolliert.</span><span class="sxs-lookup"><span data-stu-id="1fee9-149">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="1fee9-150">Blazor-Apps protokollieren standardmäßig in die Konsolenausgabe beim Konsolenprotokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="1fee9-150">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="1fee9-151">Überlegen Sie, ob Sie für die Protokollierung besser einen permanenteren Speicherort bei einem Anbieter verwenden, der Protokollgröße und Protokollrotation verwaltet.</span><span class="sxs-lookup"><span data-stu-id="1fee9-151">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="1fee9-152">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="1fee9-152">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="1fee9-153">Während der Entwicklung sendet Blazor zur Unterstützung beim Debuggen in der Regel alle Ausnahmedetails an die Konsole des Browsers.</span><span class="sxs-lookup"><span data-stu-id="1fee9-153">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="1fee9-154">In einer Produktionsumgebung sind ausführliche Fehler in der Konsole des Browsers standardmäßig deaktiviert. Das bedeutet, dass Fehler nicht an Clients gesendet werden. Es werden jedoch alle Details der Ausnahme serverseitig protokolliert.</span><span class="sxs-lookup"><span data-stu-id="1fee9-154">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="1fee9-155">Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="1fee9-155">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="1fee9-156">Sie müssen entscheiden, welche Vorfälle protokolliert werden sollen. Zudem müssen Sie den Schweregrad für protokollierte Vorfälle festlegen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-156">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="1fee9-157">Feindliche Benutzer können Fehler möglicherweise absichtlich auslöst.</span><span class="sxs-lookup"><span data-stu-id="1fee9-157">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="1fee9-158">Protokollieren Sie beispielsweise keinen Vorfall aus einem Fehler, bei dem eine unbekannte `ProductId` in der URL einer Komponente angegeben wurde, über die Produktdetails angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="1fee9-158">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="1fee9-159">Nicht alle Fehler sollten als zu protokollierende Vorfälle mit hohem Schweregrad behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="1fee9-159">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="1fee9-160">Weitere Informationen finden Sie unter <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="1fee9-160">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="1fee9-161">Stellen, an denen Fehler auftreten können</span><span class="sxs-lookup"><span data-stu-id="1fee9-161">Places where errors may occur</span></span>

<span data-ttu-id="1fee9-162">Framework- und App-Code kann an den folgenden Stellen Ausnahmefehler auslösen:</span><span class="sxs-lookup"><span data-stu-id="1fee9-162">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="1fee9-163">Komponenteninstanziierung</span><span class="sxs-lookup"><span data-stu-id="1fee9-163">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="1fee9-164">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="1fee9-164">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="1fee9-165">Renderinglogik</span><span class="sxs-lookup"><span data-stu-id="1fee9-165">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="1fee9-166">Ereignishandler</span><span class="sxs-lookup"><span data-stu-id="1fee9-166">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="1fee9-167">Beseitigung von Komponenten</span><span class="sxs-lookup"><span data-stu-id="1fee9-167">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="1fee9-168">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="1fee9-168">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="1fee9-169">Blazor Server-Rerendering</span><span class="sxs-lookup"><span data-stu-id="1fee9-169">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="1fee9-170">Die genannten Ausnahmefehler werden in den folgenden Abschnitten dieses Artikels beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1fee9-170">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="1fee9-171">Komponenteninstanziierung</span><span class="sxs-lookup"><span data-stu-id="1fee9-171">Component instantiation</span></span>

<span data-ttu-id="1fee9-172">Wenn durch Blazor eine Instanz einer Komponente erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="1fee9-172">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="1fee9-173">Wird der Konstruktor der Komponente aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-173">The component's constructor is invoked.</span></span>
* <span data-ttu-id="1fee9-174">Werden die Konstruktoren von Nicht-Singleton-DI-Diensten aufgerufen, die dem Konstruktor der Komponente mit der Anweisung [`@inject`](xref:mvc/views/razor#inject) oder mit dem Attribut [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1fee9-174">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="1fee9-175">Bei einer Blazor Server-Leitung tritt ein Fehler auf, wenn ein ausgeführter Konstruktor oder ein Setter für eine `[Inject]`-Eigenschaft einen Ausnahmefehler auslöst.</span><span class="sxs-lookup"><span data-stu-id="1fee9-175">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="1fee9-176">Die Ausnahme ist schwerwiegend, weil die Komponente durch das Framework nicht instanziiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="1fee9-176">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="1fee9-177">Wenn eine Konstruktorlogik Ausnahmen auslösen kann, muss die App die Ausnahmen mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung abfangen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-177">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="1fee9-178">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="1fee9-178">Lifecycle methods</span></span>

<span data-ttu-id="1fee9-179">Während der Lebensdauer einer Komponente ruft Blazor die folgenden [Lebenszyklusmethoden](xref:blazor/components/lifecycle) aufrufen:</span><span class="sxs-lookup"><span data-stu-id="1fee9-179">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="1fee9-180">Wenn eine Lebenszyklusmethode synchron oder asynchron eine Ausnahme auslöst, ist die Ausnahme für eine Blazor Server-Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1fee9-180">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="1fee9-181">Damit Komponenten Fehler in Lebenszyklusmethoden behandeln können, fügen Sie eine Fehlerbehandlungslogik hinzu.</span><span class="sxs-lookup"><span data-stu-id="1fee9-181">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="1fee9-182">Im folgenden Beispiel, in dem <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> eine Methode zum Aufrufen eines Produkts aufruft:</span><span class="sxs-lookup"><span data-stu-id="1fee9-182">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="1fee9-183">Wird eine Ausnahme, die in der `ProductRepository.GetProductByIdAsync`-Methode ausgelöst wird, durch eine [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung behandelt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-183">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="1fee9-184">Wenn der `catch`-Block ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="1fee9-184">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="1fee9-185">wird `loadFailed` auf `true` festgelegt, sodass dem Benutzer eine Fehlermeldung angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="1fee9-185">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="1fee9-186">wird der Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="1fee9-186">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="1fee9-187">Renderinglogik</span><span class="sxs-lookup"><span data-stu-id="1fee9-187">Rendering logic</span></span>

<span data-ttu-id="1fee9-188">Das deklarative Markup in einer `.razor`-Komponentendatei wird in eine C#-Methode namens <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> kompiliert.</span><span class="sxs-lookup"><span data-stu-id="1fee9-188">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="1fee9-189">Beim Rendern einer Komponente wird von <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> eine Datenstruktur ausgeführt und erstellt, die Elemente, Text und untergeordnete Komponenten der gerenderten Komponente beschreibt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-189">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="1fee9-190">Die Renderinglogik kann eine Ausnahme auslösen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-190">Rendering logic can throw an exception.</span></span> <span data-ttu-id="1fee9-191">Ein Beispiel für dieses Szenario tritt auf, wenn `@someObject.PropertyName` ausgewertet wird, `@someObject` jedoch `null` ist.</span><span class="sxs-lookup"><span data-stu-id="1fee9-191">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="1fee9-192">Ein von einer Renderinglogik ausgelöster Ausnahmefehler ist für eine Blazor Server-Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1fee9-192">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="1fee9-193">Um in einer Renderinglogik eine Nullverweisausnahme zu vermeiden, prüfen Sie vor dem Zugriff auf die entsprechenden Member, ob ein `null`-Objekt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1fee9-193">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="1fee9-194">Im folgenden Beispiel wird auf die `person.Address`-Eigenschaften nicht zugegriffen, wenn `person.Address` `null` ist:</span><span class="sxs-lookup"><span data-stu-id="1fee9-194">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="1fee9-195">Für den obigen Code wird angenommen, dass `person` nicht `null` ist.</span><span class="sxs-lookup"><span data-stu-id="1fee9-195">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="1fee9-196">Häufig wird durch die Codestruktur sichergestellt, dass ein Objekt zu dem Zeitpunkt vorhanden ist, zu dem die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="1fee9-196">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="1fee9-197">In diesen Fällen muss nicht geprüft werden, ob `null` in der Renderinglogik vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1fee9-197">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="1fee9-198">Im obigen Beispiel kann garantiert werden, dass `person` vorhanden ist, da `person` beim Instanziieren der Komponente erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="1fee9-198">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="1fee9-199">Ereignishandler</span><span class="sxs-lookup"><span data-stu-id="1fee9-199">Event handlers</span></span>

<span data-ttu-id="1fee9-200">Clientseitiger Code löst Aufrufe von C#-Code aus, wenn Ereignishandler mit folgenden Elementen erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="1fee9-200">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="1fee9-201">Mit anderen `@on...`-Attributen</span><span class="sxs-lookup"><span data-stu-id="1fee9-201">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="1fee9-202">Der Ereignishandlercode löst in diesen Szenarios möglicherweise einen Ausnahmefehler aus.</span><span class="sxs-lookup"><span data-stu-id="1fee9-202">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="1fee9-203">Wenn ein Ereignishandler einen Ausnahmefehler (z. B. einen Datenbankabfragefehler) auslöst, ist die Ausnahme für eine Blazor Server-Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1fee9-203">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="1fee9-204">Für den Fall, dass durch die App Code aufgerufen wird, der aus externen Gründen einen Fehler verursachen könnte, müssen Ausnahmen mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung abgefangen werden.</span><span class="sxs-lookup"><span data-stu-id="1fee9-204">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="1fee9-205">Wenn die Ausnahme nicht durch Benutzercode abgefangen und behandelt wird, wird sie durch das Framework protokolliert, und die Leitung wird beendet.</span><span class="sxs-lookup"><span data-stu-id="1fee9-205">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="1fee9-206">Beseitigung von Komponenten</span><span class="sxs-lookup"><span data-stu-id="1fee9-206">Component disposal</span></span>

<span data-ttu-id="1fee9-207">Eine Komponente kann von der Benutzeroberfläche entfernt werden, weil der Benutzer beispielsweise zu einer anderen Seite navigiert ist.</span><span class="sxs-lookup"><span data-stu-id="1fee9-207">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="1fee9-208">Wenn eine Komponente, die <xref:System.IDisposable?displayProperty=fullName> implementiert, von der Benutzeroberfläche entfernt wird, ruft das Framework die <xref:System.IDisposable.Dispose%2A>-Methode der Komponente auf.</span><span class="sxs-lookup"><span data-stu-id="1fee9-208">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="1fee9-209">Wenn die `Dispose`-Methode der Komponente einen Ausnahmefehler auslöst, ist die Ausnahme für eine Blazor Server-Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1fee9-209">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="1fee9-210">Wenn eine Beseitigungslogik Ausnahmen auslösen kann, muss die App die Ausnahmen mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung abfangen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-210">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="1fee9-211">Weitere Informationen zur Beseitigung von Komponenten finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="1fee9-211">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="1fee9-212">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="1fee9-212">JavaScript interop</span></span>

<span data-ttu-id="1fee9-213">Mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> kann .NET-Code die JavaScript-Laufzeit im Browser des Benutzers asynchron aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-213"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="1fee9-214">Die folgenden Bedingungen gelten für die Fehlerbehandlung mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="1fee9-214">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="1fee9-215">Wenn ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> synchron fehlschlägt, tritt eine .NET-Ausnahme auf.</span><span class="sxs-lookup"><span data-stu-id="1fee9-215">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="1fee9-216">Ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> kann beispielsweise fehlschlagen, wenn die bereitgestellten Argumente nicht serialisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="1fee9-216">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="1fee9-217">Die Ausnahme muss vom Entwicklercode abgefangen werden.</span><span class="sxs-lookup"><span data-stu-id="1fee9-217">Developer code must catch the exception.</span></span> <span data-ttu-id="1fee9-218">Wenn eine Ausnahme nicht von App-Code in einem Ereignishandler oder in der Lebenszyklusmethode einer Komponente behandelt wird, ist die resultierende Ausnahme für eine Blazor Server-Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1fee9-218">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="1fee9-219">Wenn ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> asynchron fehlschlägt, schlägt die .NET <xref:System.Threading.Tasks.Task> fehl.</span><span class="sxs-lookup"><span data-stu-id="1fee9-219">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="1fee9-220">Ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> kann beispielsweise fehlschlagen, wenn der JavaScript-Code eine Ausnahme auslöst oder eine `Promise` zurückgibt, die als `rejected` abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="1fee9-220">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="1fee9-221">Die Ausnahme muss vom Entwicklercode abgefangen werden.</span><span class="sxs-lookup"><span data-stu-id="1fee9-221">Developer code must catch the exception.</span></span> <span data-ttu-id="1fee9-222">Wenn Sie den [`await`](/dotnet/csharp/language-reference/keywords/await)-Operator verwenden, sollten Sie in Erwägung ziehen, den Methodenaufruf mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung zu umschließen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-222">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="1fee9-223">Tun Sie dies nicht, führt der fehlerhafte Code zu einem Ausnahmefehler, der für eine Blazor Server-Leitung schwerwiegend ist.</span><span class="sxs-lookup"><span data-stu-id="1fee9-223">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="1fee9-224">Ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> muss standardmäßig innerhalb eines bestimmten Zeitraums abgeschlossen werden, da ansonsten für den Aufruf ein Timeout auftritt. Die Standardwert für das Zeitlimit beträgt eine Minute.</span><span class="sxs-lookup"><span data-stu-id="1fee9-224">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="1fee9-225">Mit dem Zeitlimit wird der Code vor dem Verlust der Netzwerkkonnektivität oder vor JavaScript-Code geschützt, der keine Abschlussmeldung sendet.</span><span class="sxs-lookup"><span data-stu-id="1fee9-225">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="1fee9-226">Wenn beim Aufruf ein Timeout auftritt, schlägt die resultierende <xref:System.Threading.Tasks> mit einer <xref:System.OperationCanceledException>fehl.</span><span class="sxs-lookup"><span data-stu-id="1fee9-226">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="1fee9-227">Die Ausnahme wird abgefangen und mit Protokollierung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="1fee9-227">Trap and process the exception with logging.</span></span>

<span data-ttu-id="1fee9-228">Ähnlich kann JavaScript-Code Aufrufe von .NET-Methoden initiieren, die durch das [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript)-Attribut angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="1fee9-228">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="1fee9-229">Wenn diese .NET-Methoden einen Ausnahmefehler auslösen:</span><span class="sxs-lookup"><span data-stu-id="1fee9-229">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="1fee9-230">wird die Ausnahme nicht als schwerwiegend für eine Blazor Server-Leitung behandelt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-230">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="1fee9-231">wird die JavaScript-seitige `Promise` abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-231">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="1fee9-232">Sie können Fehlerbehandlungscode auf der .NET-Seite oder auf der JavaScript-Seite des Methodenaufrufs verwenden.</span><span class="sxs-lookup"><span data-stu-id="1fee9-232">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="1fee9-233">Weitere Informationen finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="1fee9-233">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a><span data-ttu-id="1fee9-234">Blazor Server-Prerendering</span><span class="sxs-lookup"><span data-stu-id="1fee9-234">Blazor Server prerendering</span></span>

<span data-ttu-id="1fee9-235">Blazor-Komponenten können mit dem [Komponententaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) vorab gerendert werden, sodass das gerenderte HTML-Markup als Teil der ursprünglichen HTTP-Anforderung des Benutzers zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="1fee9-235">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="1fee9-236">Dies funktioniert durch:</span><span class="sxs-lookup"><span data-stu-id="1fee9-236">This works by:</span></span>

* <span data-ttu-id="1fee9-237">Erstellen einer neuen Leitung für alle vorab gerenderten Komponenten, die Teil derselben Seite sind</span><span class="sxs-lookup"><span data-stu-id="1fee9-237">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="1fee9-238">Generieren des ursprünglichen HTML-Codes</span><span class="sxs-lookup"><span data-stu-id="1fee9-238">Generating the initial HTML.</span></span>
* <span data-ttu-id="1fee9-239">Behandeln der Leitung als `disconnected`, bis der Browser des Benutzers eine SignalR-Verbindung zurück zum selben Server einrichtet.</span><span class="sxs-lookup"><span data-stu-id="1fee9-239">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="1fee9-240">Wenn die Verbindung hergestellt wird, wird die Interaktivität in der Leitung fortgesetzt und das HTML-Markup der Komponenten wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="1fee9-240">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="1fee9-241">Wenn von einer Komponente während des Prerenderings ein Ausnahmefehler beispielswiese während einer Lebenszyklusmethode oder in der Renderinglogik ausgelöst wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1fee9-241">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="1fee9-242">Die Ausnahme ist für die Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1fee9-242">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="1fee9-243">Die Ausnahme wird vom <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>-Taghilfsprogramm in der Aufrufliste weiter oben ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="1fee9-243">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="1fee9-244">Daher schlägt die gesamte HTTP-Anforderung fehl, es sei denn, die Ausnahme wird explizit vom Entwicklercode abgefangen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-244">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="1fee9-245">Wenn das Prerendering unter normalen Umständen fehlschlägt, ist es nicht sinnvoll, mit dem Erstellen und Rendern der Komponente fortzufahren, da eine funktionierende Komponente nicht gerendert werden kann.</span><span class="sxs-lookup"><span data-stu-id="1fee9-245">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="1fee9-246">Wenn während des Prerenderings auftretende Fehler toleriert werden sollen, muss sich die Fehlerbehandlungslogik in einer Komponente befinden, die möglicherweise Ausnahmen auslöst.</span><span class="sxs-lookup"><span data-stu-id="1fee9-246">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="1fee9-247">Verwenden Sie [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisungen mit Fehlerbehandlung und Fehlerprotokollierung.</span><span class="sxs-lookup"><span data-stu-id="1fee9-247">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="1fee9-248">Statt das <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>-Tag-Hilfsprogramm in einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung zu umschließen, fügen Sie eine Fehlerbehandlungslogik in die vom <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>-Tag-Hilfsprogramm gerenderte Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="1fee9-248">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="1fee9-249">Erweiterte Szenarien</span><span class="sxs-lookup"><span data-stu-id="1fee9-249">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="1fee9-250">Rekursives Rendering</span><span class="sxs-lookup"><span data-stu-id="1fee9-250">Recursive rendering</span></span>

<span data-ttu-id="1fee9-251">Komponenten können rekursiv geschachtelt sein.</span><span class="sxs-lookup"><span data-stu-id="1fee9-251">Components can be nested recursively.</span></span> <span data-ttu-id="1fee9-252">Dies ist nützlich, um rekursive Datenstrukturen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-252">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="1fee9-253">So kann eine `TreeNode`-Komponente beispielsweise weitere `TreeNode`-Komponenten für die einzelnen untergeordneten Elemente des Knotens rendern.</span><span class="sxs-lookup"><span data-stu-id="1fee9-253">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="1fee9-254">Vermeiden Sie beim rekursiven Rendern Codemuster, die zu einer Endlosschleife führen:</span><span class="sxs-lookup"><span data-stu-id="1fee9-254">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="1fee9-255">Vermeiden Sie rekursives Rendering bei einer Datenstruktur, die einen Zyklus enthält.</span><span class="sxs-lookup"><span data-stu-id="1fee9-255">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="1fee9-256">Rendern Sie beispielsweise keinen Strukturknoten, dessen untergeordnete Knoten sich selbst enthalten.</span><span class="sxs-lookup"><span data-stu-id="1fee9-256">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="1fee9-257">Erstellen Sie keine Layoutkette, die einen Zyklus enthält.</span><span class="sxs-lookup"><span data-stu-id="1fee9-257">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="1fee9-258">Erstellen Sie beispielsweise kein Layout, das sein eigenes Layout ist.</span><span class="sxs-lookup"><span data-stu-id="1fee9-258">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="1fee9-259">Lassen Sie nicht zu, dass ein Endbenutzer Rekursionsinvarianten (Regeln) durch eine böswillige Dateneingabe oder JavaScript-Interoperabilitätsaufrufe verletzt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-259">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="1fee9-260">Endlosschleifen durch Rendering:</span><span class="sxs-lookup"><span data-stu-id="1fee9-260">Infinite loops during rendering:</span></span>

* <span data-ttu-id="1fee9-261">sorgt dafür, dass der Renderingprozess unendlich fortgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="1fee9-261">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="1fee9-262">entspricht dem Erstellen einer nicht abgeschlossenen Schleife.</span><span class="sxs-lookup"><span data-stu-id="1fee9-262">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="1fee9-263">In diesen Szenarios tritt ein Fehler bei einer betroffenen Blazor Server-Leitung auf, und der Thread versucht in der Regel Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1fee9-263">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="1fee9-264">Unbegrenzt so viel CPU-Zeit wie vom Betriebssystem zulässig zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-264">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="1fee9-265">Eine unbegrenzte Menge an Serverarbeitsspeicher zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-265">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="1fee9-266">Das Belegen einer unbegrenzte Menge an Arbeitsspeicher entspricht einem Szenario, bei dem eine nicht beendete Schleife einer Auflistung bei jeder Iteration Einträge hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-266">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="1fee9-267">Wenn Sie Muster mit Endlosrekursionen vermeiden möchten, müssen Sie sicherstellen, dass der Renderingcode geeignete Beendigungsbedingungen enthält.</span><span class="sxs-lookup"><span data-stu-id="1fee9-267">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="1fee9-268">Benutzerdefinierte Renderstrukturlogik</span><span class="sxs-lookup"><span data-stu-id="1fee9-268">Custom render tree logic</span></span>

<span data-ttu-id="1fee9-269">Die meisten Blazor-Komponenten werden als `.razor`-Dateien implementiert und kompiliert, um eine Logik zu erzeugen, die einen <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> verwendet, um dessen Ausgabe zu rendern.</span><span class="sxs-lookup"><span data-stu-id="1fee9-269">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="1fee9-270">Ein Entwickler kann mit prozeduralem C#-Code eine <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Logik manuell implementieren.</span><span class="sxs-lookup"><span data-stu-id="1fee9-270">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="1fee9-271">Weitere Informationen finden Sie unter <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="1fee9-271">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="1fee9-272">Die Verwendung einer manuellen Buildlogik für die Renderstruktur gilt als erweitertes und unsicheres Szenario, das für die allgemeine Komponentenentwicklung nicht empfohlen wird.</span><span class="sxs-lookup"><span data-stu-id="1fee9-272">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="1fee9-273">Beim Schreiben von <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Code muss der Entwickler die Richtigkeit des Codes garantieren.</span><span class="sxs-lookup"><span data-stu-id="1fee9-273">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="1fee9-274">Der Entwickler muss beispielsweise Folgendes sicherstellen:</span><span class="sxs-lookup"><span data-stu-id="1fee9-274">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="1fee9-275">Aufrufe von <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> und <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> sind ordnungsgemäß ausgeglichen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-275">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="1fee9-276">Attribute werden nur an den richtigen Stellen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1fee9-276">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="1fee9-277">Eine fehlerhafte Buildlogik für die Renderstruktur kann ein willkürliches undefiniertes Verhalten wie Abstürze, Serverabstürze und Sicherheitsrisiken verursachen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-277">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="1fee9-278">Eine manuelle Buildlogik für die Renderstruktur ist genauso komplex und birgt dasselbe Maß an *Gefahren* wie das manuelle Schreiben von Assemblycode oder MSIL-Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="1fee9-278">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
