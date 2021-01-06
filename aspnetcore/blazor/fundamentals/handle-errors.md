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
ms.openlocfilehash: c789928252417ef1cf95c60deb7edef24d58126e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93055996"
---
# <a name="handle-errors-in-aspnet-core-no-locblazor-apps"></a><span data-ttu-id="1f855-103">Fehlerbehandlung in ASP.NET Core Blazor-Apps</span><span class="sxs-lookup"><span data-stu-id="1f855-103">Handle errors in ASP.NET Core Blazor apps</span></span>

<span data-ttu-id="1f855-104">Von [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="1f855-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="1f855-105">In diesem Artikel wird beschrieben, wie Blazor Ausnahmefehler behandelt und wie Sie Apps entwickeln können, die Fehler erkennen und behandeln.</span><span class="sxs-lookup"><span data-stu-id="1f855-105">This article describes how Blazor manages unhandled exceptions and how to develop apps that detect and handle errors.</span></span>

## <a name="detailed-errors-during-development"></a><span data-ttu-id="1f855-106">Ausführliche Fehler bei der Entwicklung</span><span class="sxs-lookup"><span data-stu-id="1f855-106">Detailed errors during development</span></span>

<span data-ttu-id="1f855-107">Wenn eine Blazor-App während der Entwicklung nicht ordnungsgemäß funktioniert, erhalten Sie nun ausführliche Fehlerinformationen von der App, die Sie beim Beheben des Problems unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1f855-107">When a Blazor app isn't functioning properly during development, receiving detailed error information from the app assists in troubleshooting and fixing the issue.</span></span> <span data-ttu-id="1f855-108">Wenn ein Fehler auftritt, zeigen Blazor-Apps eine goldene Leiste am unteren Rand der Anzeige an:</span><span class="sxs-lookup"><span data-stu-id="1f855-108">When an error occurs, Blazor apps display a gold bar at the bottom of the screen:</span></span>

* <span data-ttu-id="1f855-109">Während der Entwicklung leitet die goldene Leiste Sie an die Browserkonsole weiter, in der die Ausnahme angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="1f855-109">During development, the gold bar directs you to the browser console, where you can see the exception.</span></span>
* <span data-ttu-id="1f855-110">In der Produktion benachrichtigt die goldene Leiste den Benutzer darüber, dass ein Fehler aufgetreten ist, und empfiehlt eine Aktualisierung des Browsers.</span><span class="sxs-lookup"><span data-stu-id="1f855-110">In production, the gold bar notifies the user that an error has occurred and recommends refreshing the browser.</span></span>

<span data-ttu-id="1f855-111">Die Benutzeroberfläche für diese Fehlerbehandlung ist Teil der Blazor-Projektvorlagen.</span><span class="sxs-lookup"><span data-stu-id="1f855-111">The UI for this error handling experience is part of the Blazor project templates.</span></span>

<span data-ttu-id="1f855-112">Passen Sie in einer Blazor WebAssembly-App die Darstellung in der `wwwroot/index.html`-Datei an:</span><span class="sxs-lookup"><span data-stu-id="1f855-112">In a Blazor WebAssembly app, customize the experience in the `wwwroot/index.html` file:</span></span>

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

<span data-ttu-id="1f855-113">Passen Sie in einer Blazor Server-App die Darstellung in der `Pages/_Host.cshtml`-Datei an:</span><span class="sxs-lookup"><span data-stu-id="1f855-113">In a Blazor Server app, customize the experience in the `Pages/_Host.cshtml` file:</span></span>

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

<span data-ttu-id="1f855-114">Das `blazor-error-ui`-Element wird von den Stilen in den Blazor-Vorlagen (`wwwroot/css/app.css` oder `wwwroot/css/site.css`) verborgen und nur angezeigt, wenn ein Fehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="1f855-114">The `blazor-error-ui` element is hidden by the styles included in the Blazor templates (`wwwroot/css/app.css` or `wwwroot/css/site.css`) and then shown when an error occurs:</span></span>

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

## <a name="how-a-no-locblazor-server-app-reacts-to-unhandled-exceptions"></a><span data-ttu-id="1f855-115">Reaktion einer Blazor Server-App auf Ausnahmefehler</span><span class="sxs-lookup"><span data-stu-id="1f855-115">How a Blazor Server app reacts to unhandled exceptions</span></span>

<span data-ttu-id="1f855-116">Blazor Server ist ein zustandsbehaftetes Framework.</span><span class="sxs-lookup"><span data-stu-id="1f855-116">Blazor Server is a stateful framework.</span></span> <span data-ttu-id="1f855-117">Während Benutzer mit einer App interagieren, besteht eine Verbindung mit dem Server, die als *Leitung* bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="1f855-117">While users interact with an app, they maintain a connection to the server known as a *circuit*.</span></span> <span data-ttu-id="1f855-118">Die Leitung enthält aktive Instanzen von Komponenten und weist zahlreiche andere Aspekte zum Zustand auf wie:</span><span class="sxs-lookup"><span data-stu-id="1f855-118">The circuit holds active component instances, plus many other aspects of state, such as:</span></span>

* <span data-ttu-id="1f855-119">Die zuletzt gerenderte Ausgabe von Komponenten</span><span class="sxs-lookup"><span data-stu-id="1f855-119">The most recent rendered output of components.</span></span>
* <span data-ttu-id="1f855-120">Die aktuellen Delegate zur Ereignisbehandlung, die durch clientseitige Ereignisse ausgelöst werden können</span><span class="sxs-lookup"><span data-stu-id="1f855-120">The current set of event-handling delegates that could be triggered by client-side events.</span></span>

<span data-ttu-id="1f855-121">Wenn ein Benutzer die App in mehreren Registerkarten eines Browsers öffnet, bestehen mehrere unabhängige Leitungen.</span><span class="sxs-lookup"><span data-stu-id="1f855-121">If a user opens the app in multiple browser tabs, they have multiple independent circuits.</span></span>

<span data-ttu-id="1f855-122">Die meisten Ausnahmefehler werden von Blazor als für die Leitung schwerwiegende Fehler behandelt.</span><span class="sxs-lookup"><span data-stu-id="1f855-122">Blazor treats most unhandled exceptions as fatal to the circuit where they occur.</span></span> <span data-ttu-id="1f855-123">Wenn eine Leitung aufgrund eines Ausnahmefehlers beendet wird, kann der Benutzer nur dann mit der App weiter interagieren, wenn die Seite nochmals geladen und so eine neue Leitung erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="1f855-123">If a circuit is terminated due to an unhandled exception, the user can only continue to interact with the app by reloading the page to create a new circuit.</span></span> <span data-ttu-id="1f855-124">Leitungen außerhalb der beendeten Leitung, bei denen es sich um Leitungen für andere Benutzer oder andere Browserregisterkarten handelt, sind davon nicht betroffen.</span><span class="sxs-lookup"><span data-stu-id="1f855-124">Circuits outside of the one that's terminated, which are circuits for other users or other browser tabs, aren't affected.</span></span> <span data-ttu-id="1f855-125">Dieses Szenario ähnelt einer Desktop-App, die abstürzt.</span><span class="sxs-lookup"><span data-stu-id="1f855-125">This scenario is similar to a desktop app that crashes.</span></span> <span data-ttu-id="1f855-126">Die abgestürzte App muss neu gestartet werden, aber andere Apps sind nicht betroffen.</span><span class="sxs-lookup"><span data-stu-id="1f855-126">The crashed app must be restarted, but other apps aren't affected.</span></span>

<span data-ttu-id="1f855-127">Eine Leitung wird beendet, wenn aus folgenden Gründen ein Ausnahmefehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="1f855-127">A circuit is terminated when an unhandled exception occurs for the following reasons:</span></span>

* <span data-ttu-id="1f855-128">Bei einem Ausnahmefehler wird die Leitung häufig in einen nicht definierten Zustand versetzt.</span><span class="sxs-lookup"><span data-stu-id="1f855-128">An unhandled exception often leaves the circuit in an undefined state.</span></span>
* <span data-ttu-id="1f855-129">Nach einem Ausnahmefehler kann ein normales Funktionieren der App nicht mehr sichergestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1f855-129">The app's normal operation can't be guaranteed after an unhandled exception.</span></span>
* <span data-ttu-id="1f855-130">Wenn die Leitung weiter bestehen bleibt, können für die App Sicherheitsrisiken bestehen.</span><span class="sxs-lookup"><span data-stu-id="1f855-130">Security vulnerabilities may appear in the app if the circuit continues.</span></span>

## <a name="manage-unhandled-exceptions-in-developer-code"></a><span data-ttu-id="1f855-131">Behandeln von Ausnahmefehlern in Entwicklercode</span><span class="sxs-lookup"><span data-stu-id="1f855-131">Manage unhandled exceptions in developer code</span></span>

<span data-ttu-id="1f855-132">Damit die Ausführung einer App nach einem Fehler fortgesetzt werden kann, muss die App eine Fehlerbehandlungslogik enthalten.</span><span class="sxs-lookup"><span data-stu-id="1f855-132">For an app to continue after an error, the app must have error handling logic.</span></span> <span data-ttu-id="1f855-133">In späteren Abschnitten dieses Artikels werden mögliche Quellen für Ausnahmefehler beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1f855-133">Later sections of this article describe potential sources of unhandled exceptions.</span></span>

<span data-ttu-id="1f855-134">Rendern Sie in einer Produktionsumgebung keine Ausnahmemeldungen des Frameworks oder Stapelüberwachungen in der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="1f855-134">In production, don't render framework exception messages or stack traces in the UI.</span></span> <span data-ttu-id="1f855-135">Durch Rendern von Ausnahmemeldungen oder Stapelüberwachungen kann Folgendes geschehen:</span><span class="sxs-lookup"><span data-stu-id="1f855-135">Rendering exception messages or stack traces could:</span></span>

* <span data-ttu-id="1f855-136">Vertrauliche Informationen werden gegenüber Endbenutzern offengelegt.</span><span class="sxs-lookup"><span data-stu-id="1f855-136">Disclose sensitive information to end users.</span></span>
* <span data-ttu-id="1f855-137">Böswilligen Benutzern wird geholfen, Schwachstellen in einer App zu erkennen, die die Sicherheit von Server, App oder Netzwerk beeinträchtigen können.</span><span class="sxs-lookup"><span data-stu-id="1f855-137">Help a malicious user discover weaknesses in an app that can compromise the security of the app, server, or network.</span></span>

## <a name="log-errors-with-a-persistent-provider"></a><span data-ttu-id="1f855-138">Protokollieren von Fehlern bei einem permanenten Anbieter</span><span class="sxs-lookup"><span data-stu-id="1f855-138">Log errors with a persistent provider</span></span>

<span data-ttu-id="1f855-139">Im Fall eines Ausnahmefehlers wird die Ausnahme in den im Dienstcontainer konfigurierten<xref:Microsoft.Extensions.Logging.ILogger>-Instanzen protokolliert.</span><span class="sxs-lookup"><span data-stu-id="1f855-139">If an unhandled exception occurs, the exception is logged to <xref:Microsoft.Extensions.Logging.ILogger> instances configured in the service container.</span></span> <span data-ttu-id="1f855-140">Blazor-Apps protokollieren standardmäßig in die Konsolenausgabe beim Konsolenprotokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="1f855-140">By default, Blazor apps log to console output with the Console Logging Provider.</span></span> <span data-ttu-id="1f855-141">Überlegen Sie, ob Sie für die Protokollierung besser einen permanenteren Speicherort bei einem Anbieter verwenden, der Protokollgröße und Protokollrotation verwaltet.</span><span class="sxs-lookup"><span data-stu-id="1f855-141">Consider logging to a more permanent location with a provider that manages log size and log rotation.</span></span> <span data-ttu-id="1f855-142">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="1f855-142">For more information, see <xref:fundamentals/logging/index>.</span></span>

<span data-ttu-id="1f855-143">Während der Entwicklung sendet Blazor zur Unterstützung beim Debuggen in der Regel alle Ausnahmedetails an die Konsole des Browsers.</span><span class="sxs-lookup"><span data-stu-id="1f855-143">During development, Blazor usually sends the full details of exceptions to the browser's console to aid in debugging.</span></span> <span data-ttu-id="1f855-144">In einer Produktionsumgebung sind ausführliche Fehler in der Konsole des Browsers standardmäßig deaktiviert. Das bedeutet, dass Fehler nicht an Clients gesendet werden. Es werden jedoch alle Details der Ausnahme serverseitig protokolliert.</span><span class="sxs-lookup"><span data-stu-id="1f855-144">In production, detailed errors in the browser's console are disabled by default, which means that errors aren't sent to clients but the exception's full details are still logged server-side.</span></span> <span data-ttu-id="1f855-145">Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="1f855-145">For more information, see <xref:fundamentals/error-handling>.</span></span>

<span data-ttu-id="1f855-146">Sie müssen entscheiden, welche Vorfälle protokolliert werden sollen. Zudem müssen Sie den Schweregrad für protokollierte Vorfälle festlegen.</span><span class="sxs-lookup"><span data-stu-id="1f855-146">You must decide which incidents to log and the level of severity of logged incidents.</span></span> <span data-ttu-id="1f855-147">Feindliche Benutzer können Fehler möglicherweise absichtlich auslöst.</span><span class="sxs-lookup"><span data-stu-id="1f855-147">Hostile users might be able to trigger errors deliberately.</span></span> <span data-ttu-id="1f855-148">Protokollieren Sie beispielsweise keinen Vorfall aus einem Fehler, bei dem eine unbekannte `ProductId` in der URL einer Komponente angegeben wurde, über die Produktdetails angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="1f855-148">For example, don't log an incident from an error where an unknown `ProductId` is supplied in the URL of a component that displays product details.</span></span> <span data-ttu-id="1f855-149">Nicht alle Fehler sollten als zu protokollierende Vorfälle mit hohem Schweregrad behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="1f855-149">Not all errors should be treated as high-severity incidents for logging.</span></span>

<span data-ttu-id="1f855-150">Weitere Informationen finden Sie unter <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="1f855-150">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="places-where-errors-may-occur"></a><span data-ttu-id="1f855-151">Stellen, an denen Fehler auftreten können</span><span class="sxs-lookup"><span data-stu-id="1f855-151">Places where errors may occur</span></span>

<span data-ttu-id="1f855-152">Framework- und App-Code kann an den folgenden Stellen Ausnahmefehler auslösen:</span><span class="sxs-lookup"><span data-stu-id="1f855-152">Framework and app code may trigger unhandled exceptions in any of the following locations:</span></span>

* [<span data-ttu-id="1f855-153">Komponenteninstanziierung</span><span class="sxs-lookup"><span data-stu-id="1f855-153">Component instantiation</span></span>](#component-instantiation)
* [<span data-ttu-id="1f855-154">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="1f855-154">Lifecycle methods</span></span>](#lifecycle-methods)
* [<span data-ttu-id="1f855-155">Renderinglogik</span><span class="sxs-lookup"><span data-stu-id="1f855-155">Rendering logic</span></span>](#rendering-logic)
* [<span data-ttu-id="1f855-156">Ereignishandler</span><span class="sxs-lookup"><span data-stu-id="1f855-156">Event handlers</span></span>](#event-handlers)
* [<span data-ttu-id="1f855-157">Beseitigung von Komponenten</span><span class="sxs-lookup"><span data-stu-id="1f855-157">Component disposal</span></span>](#component-disposal)
* [<span data-ttu-id="1f855-158">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="1f855-158">JavaScript interop</span></span>](#javascript-interop)
* [<span data-ttu-id="1f855-159">Blazor Server-Rerendering</span><span class="sxs-lookup"><span data-stu-id="1f855-159">Blazor Server rerendering</span></span>](#blazor-server-prerendering)

<span data-ttu-id="1f855-160">Die genannten Ausnahmefehler werden in den folgenden Abschnitten dieses Artikels beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1f855-160">The preceding unhandled exceptions are described in the following sections of this article.</span></span>

### <a name="component-instantiation"></a><span data-ttu-id="1f855-161">Komponenteninstanziierung</span><span class="sxs-lookup"><span data-stu-id="1f855-161">Component instantiation</span></span>

<span data-ttu-id="1f855-162">Wenn durch Blazor eine Instanz einer Komponente erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="1f855-162">When Blazor creates an instance of a component:</span></span>

* <span data-ttu-id="1f855-163">Wird der Konstruktor der Komponente aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1f855-163">The component's constructor is invoked.</span></span>
* <span data-ttu-id="1f855-164">Werden die Konstruktoren von Nicht-Singleton-DI-Diensten aufgerufen, die dem Konstruktor der Komponente mit der Anweisung [`@inject`](xref:mvc/views/razor#inject) oder mit dem Attribut [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1f855-164">The constructors of any non-singleton DI services supplied to the component's constructor via the [`@inject`](xref:mvc/views/razor#inject) directive or the [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) attribute are invoked.</span></span>

<span data-ttu-id="1f855-165">Bei einer Blazor Server-Leitung tritt ein Fehler auf, wenn ein ausgeführter Konstruktor oder ein Setter für eine `[Inject]`-Eigenschaft einen Ausnahmefehler auslöst.</span><span class="sxs-lookup"><span data-stu-id="1f855-165">A Blazor Server circuit fails when any executed constructor or a setter for any `[Inject]` property throws an unhandled exception.</span></span> <span data-ttu-id="1f855-166">Die Ausnahme ist schwerwiegend, weil die Komponente durch das Framework nicht instanziiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="1f855-166">The exception is fatal because the framework can't instantiate the component.</span></span> <span data-ttu-id="1f855-167">Wenn eine Konstruktorlogik Ausnahmen auslösen kann, muss die App die Ausnahmen mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung abfangen.</span><span class="sxs-lookup"><span data-stu-id="1f855-167">If constructor logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

### <a name="lifecycle-methods"></a><span data-ttu-id="1f855-168">Lebenszyklusmethoden</span><span class="sxs-lookup"><span data-stu-id="1f855-168">Lifecycle methods</span></span>

<span data-ttu-id="1f855-169">Während der Lebensdauer einer Komponente ruft Blazor die folgenden [Lebenszyklusmethoden](xref:blazor/components/lifecycle) aufrufen:</span><span class="sxs-lookup"><span data-stu-id="1f855-169">During the lifetime of a component, Blazor invokes the following [lifecycle methods](xref:blazor/components/lifecycle):</span></span>

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

<span data-ttu-id="1f855-170">Wenn eine Lebenszyklusmethode synchron oder asynchron eine Ausnahme auslöst, ist die Ausnahme für eine Blazor Server-Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1f855-170">If any lifecycle method throws an exception, synchronously or asynchronously, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="1f855-171">Damit Komponenten Fehler in Lebenszyklusmethoden behandeln können, fügen Sie eine Fehlerbehandlungslogik hinzu.</span><span class="sxs-lookup"><span data-stu-id="1f855-171">For components to deal with errors in lifecycle methods, add error handling logic.</span></span>

<span data-ttu-id="1f855-172">Im folgenden Beispiel, in dem <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> eine Methode zum Aufrufen eines Produkts aufruft:</span><span class="sxs-lookup"><span data-stu-id="1f855-172">In the following example where <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> calls a method to obtain a product:</span></span>

* <span data-ttu-id="1f855-173">Wird eine Ausnahme, die in der `ProductRepository.GetProductByIdAsync`-Methode ausgelöst wird, durch eine [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung behandelt.</span><span class="sxs-lookup"><span data-stu-id="1f855-173">An exception thrown in the `ProductRepository.GetProductByIdAsync` method is handled by a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>
* <span data-ttu-id="1f855-174">Wenn der `catch`-Block ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="1f855-174">When the `catch` block is executed:</span></span>
  * <span data-ttu-id="1f855-175">wird `loadFailed` auf `true` festgelegt, sodass dem Benutzer eine Fehlermeldung angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="1f855-175">`loadFailed` is set to `true`, which is used to display an error message to the user.</span></span>
  * <span data-ttu-id="1f855-176">wird der Fehler protokolliert.</span><span class="sxs-lookup"><span data-stu-id="1f855-176">The error is logged.</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a><span data-ttu-id="1f855-177">Renderinglogik</span><span class="sxs-lookup"><span data-stu-id="1f855-177">Rendering logic</span></span>

<span data-ttu-id="1f855-178">Das deklarative Markup in einer `.razor`-Komponentendatei wird in eine C#-Methode namens <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> kompiliert.</span><span class="sxs-lookup"><span data-stu-id="1f855-178">The declarative markup in a `.razor` component file is compiled into a C# method called <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A>.</span></span> <span data-ttu-id="1f855-179">Beim Rendern einer Komponente wird von <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> eine Datenstruktur ausgeführt und erstellt, die Elemente, Text und untergeordnete Komponenten der gerenderten Komponente beschreibt.</span><span class="sxs-lookup"><span data-stu-id="1f855-179">When a component renders, <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> executes and builds up a data structure describing the elements, text, and child components of the rendered component.</span></span>

<span data-ttu-id="1f855-180">Die Renderinglogik kann eine Ausnahme auslösen.</span><span class="sxs-lookup"><span data-stu-id="1f855-180">Rendering logic can throw an exception.</span></span> <span data-ttu-id="1f855-181">Ein Beispiel für dieses Szenario tritt auf, wenn `@someObject.PropertyName` ausgewertet wird, `@someObject` jedoch `null` ist.</span><span class="sxs-lookup"><span data-stu-id="1f855-181">An example of this scenario occurs when `@someObject.PropertyName` is evaluated but `@someObject` is `null`.</span></span> <span data-ttu-id="1f855-182">Ein von einer Renderinglogik ausgelöster Ausnahmefehler ist für eine Blazor Server-Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1f855-182">An unhandled exception thrown by rendering logic is fatal to a Blazor Server circuit.</span></span>

<span data-ttu-id="1f855-183">Um in einer Renderinglogik eine Nullverweisausnahme zu vermeiden, prüfen Sie vor dem Zugriff auf die entsprechenden Member, ob ein `null`-Objekt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1f855-183">To prevent a null reference exception in rendering logic, check for a `null` object before accessing its members.</span></span> <span data-ttu-id="1f855-184">Im folgenden Beispiel wird auf die `person.Address`-Eigenschaften nicht zugegriffen, wenn `person.Address` `null` ist:</span><span class="sxs-lookup"><span data-stu-id="1f855-184">In the following example, `person.Address` properties aren't accessed if `person.Address` is `null`:</span></span>

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

<span data-ttu-id="1f855-185">Für den obigen Code wird angenommen, dass `person` nicht `null` ist.</span><span class="sxs-lookup"><span data-stu-id="1f855-185">The preceding code assumes that `person` isn't `null`.</span></span> <span data-ttu-id="1f855-186">Häufig wird durch die Codestruktur sichergestellt, dass ein Objekt zu dem Zeitpunkt vorhanden ist, zu dem die Komponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="1f855-186">Often, the structure of the code guarantees that an object exists at the time the component is rendered.</span></span> <span data-ttu-id="1f855-187">In diesen Fällen muss nicht geprüft werden, ob `null` in der Renderinglogik vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1f855-187">In those cases, it isn't necessary to check for `null` in rendering logic.</span></span> <span data-ttu-id="1f855-188">Im obigen Beispiel kann garantiert werden, dass `person` vorhanden ist, da `person` beim Instanziieren der Komponente erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="1f855-188">In the prior example, `person` might be guaranteed to exist because `person` is created when the component is instantiated.</span></span>

### <a name="event-handlers"></a><span data-ttu-id="1f855-189">Ereignishandler</span><span class="sxs-lookup"><span data-stu-id="1f855-189">Event handlers</span></span>

<span data-ttu-id="1f855-190">Clientseitiger Code löst Aufrufe von C#-Code aus, wenn Ereignishandler mit folgenden Elementen erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="1f855-190">Client-side code triggers invocations of C# code when event handlers are created using:</span></span>

* `@onclick`
* `@onchange`
* <span data-ttu-id="1f855-191">Mit anderen `@on...`-Attributen</span><span class="sxs-lookup"><span data-stu-id="1f855-191">Other `@on...` attributes</span></span>
* `@bind`

<span data-ttu-id="1f855-192">Der Ereignishandlercode löst in diesen Szenarios möglicherweise einen Ausnahmefehler aus.</span><span class="sxs-lookup"><span data-stu-id="1f855-192">Event handler code might throw an unhandled exception in these scenarios.</span></span>

<span data-ttu-id="1f855-193">Wenn ein Ereignishandler einen Ausnahmefehler (z. B. einen Datenbankabfragefehler) auslöst, ist die Ausnahme für eine Blazor Server-Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1f855-193">If an event handler throws an unhandled exception (for example, a database query fails), the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="1f855-194">Für den Fall, dass durch die App Code aufgerufen wird, der aus externen Gründen einen Fehler verursachen könnte, müssen Ausnahmen mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung abgefangen werden.</span><span class="sxs-lookup"><span data-stu-id="1f855-194">If the app calls code that could fail for external reasons, trap exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="1f855-195">Wenn die Ausnahme nicht durch Benutzercode abgefangen und behandelt wird, wird sie durch das Framework protokolliert, und die Leitung wird beendet.</span><span class="sxs-lookup"><span data-stu-id="1f855-195">If user code doesn't trap and handle the exception, the framework logs the exception and terminates the circuit.</span></span>

### <a name="component-disposal"></a><span data-ttu-id="1f855-196">Beseitigung von Komponenten</span><span class="sxs-lookup"><span data-stu-id="1f855-196">Component disposal</span></span>

<span data-ttu-id="1f855-197">Eine Komponente kann von der Benutzeroberfläche entfernt werden, weil der Benutzer beispielsweise zu einer anderen Seite navigiert ist.</span><span class="sxs-lookup"><span data-stu-id="1f855-197">A component may be removed from the UI, for example, because the user has navigated to another page.</span></span> <span data-ttu-id="1f855-198">Wenn eine Komponente, die <xref:System.IDisposable?displayProperty=fullName> implementiert, von der Benutzeroberfläche entfernt wird, ruft das Framework die <xref:System.IDisposable.Dispose%2A>-Methode der Komponente auf.</span><span class="sxs-lookup"><span data-stu-id="1f855-198">When a component that implements <xref:System.IDisposable?displayProperty=fullName> is removed from the UI, the framework calls the component's <xref:System.IDisposable.Dispose%2A> method.</span></span>

<span data-ttu-id="1f855-199">Wenn die `Dispose`-Methode der Komponente einen Ausnahmefehler auslöst, ist die Ausnahme für eine Blazor Server-Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1f855-199">If the component's `Dispose` method throws an unhandled exception, the exception is fatal to a Blazor Server circuit.</span></span> <span data-ttu-id="1f855-200">Wenn eine Beseitigungslogik Ausnahmen auslösen kann, muss die App die Ausnahmen mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung abfangen.</span><span class="sxs-lookup"><span data-stu-id="1f855-200">If disposal logic may throw exceptions, the app should trap the exceptions using a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span>

<span data-ttu-id="1f855-201">Weitere Informationen zur Beseitigung von Komponenten finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="1f855-201">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

### <a name="javascript-interop"></a><span data-ttu-id="1f855-202">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="1f855-202">JavaScript interop</span></span>

<span data-ttu-id="1f855-203">Mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> kann .NET-Code die JavaScript-Laufzeit im Browser des Benutzers asynchron aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1f855-203"><xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> allows .NET code to make asynchronous calls to the JavaScript runtime in the user's browser.</span></span>

<span data-ttu-id="1f855-204">Die folgenden Bedingungen gelten für die Fehlerbehandlung mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="1f855-204">The following conditions apply to error handling with <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:</span></span>

* <span data-ttu-id="1f855-205">Wenn ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> synchron fehlschlägt, tritt eine .NET-Ausnahme auf.</span><span class="sxs-lookup"><span data-stu-id="1f855-205">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails synchronously, a .NET exception occurs.</span></span> <span data-ttu-id="1f855-206">Ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> kann beispielsweise fehlschlagen, wenn die bereitgestellten Argumente nicht serialisiert werden können.</span><span class="sxs-lookup"><span data-stu-id="1f855-206">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the supplied arguments can't be serialized.</span></span> <span data-ttu-id="1f855-207">Die Ausnahme muss vom Entwicklercode abgefangen werden.</span><span class="sxs-lookup"><span data-stu-id="1f855-207">Developer code must catch the exception.</span></span> <span data-ttu-id="1f855-208">Wenn eine Ausnahme nicht von App-Code in einem Ereignishandler oder in der Lebenszyklusmethode einer Komponente behandelt wird, ist die resultierende Ausnahme für eine Blazor Server-Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1f855-208">If app code in an event handler or component lifecycle method doesn't handle an exception, the resulting exception is fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="1f855-209">Wenn ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> asynchron fehlschlägt, schlägt die .NET <xref:System.Threading.Tasks.Task> fehl.</span><span class="sxs-lookup"><span data-stu-id="1f855-209">If a call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> fails asynchronously, the .NET <xref:System.Threading.Tasks.Task> fails.</span></span> <span data-ttu-id="1f855-210">Ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> kann beispielsweise fehlschlagen, wenn der JavaScript-Code eine Ausnahme auslöst oder eine `Promise` zurückgibt, die als `rejected` abgeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="1f855-210">A call to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> may fail, for example, because the JavaScript-side code throws an exception or returns a `Promise` that completed as `rejected`.</span></span> <span data-ttu-id="1f855-211">Die Ausnahme muss vom Entwicklercode abgefangen werden.</span><span class="sxs-lookup"><span data-stu-id="1f855-211">Developer code must catch the exception.</span></span> <span data-ttu-id="1f855-212">Wenn Sie den [`await`](/dotnet/csharp/language-reference/keywords/await)-Operator verwenden, sollten Sie in Erwägung ziehen, den Methodenaufruf mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung zu umschließen.</span><span class="sxs-lookup"><span data-stu-id="1f855-212">If using the [`await`](/dotnet/csharp/language-reference/keywords/await) operator, consider wrapping the method call in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement with error handling and logging.</span></span> <span data-ttu-id="1f855-213">Tun Sie dies nicht, führt der fehlerhafte Code zu einem Ausnahmefehler, der für eine Blazor Server-Leitung schwerwiegend ist.</span><span class="sxs-lookup"><span data-stu-id="1f855-213">Otherwise, the failing code results in an unhandled exception that's fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="1f855-214">Ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> muss standardmäßig innerhalb eines bestimmten Zeitraums abgeschlossen werden, da ansonsten für den Aufruf ein Timeout auftritt. Die Standardwert für das Zeitlimit beträgt eine Minute.</span><span class="sxs-lookup"><span data-stu-id="1f855-214">By default, calls to <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> must complete within a certain period or else the call times out. The default timeout period is one minute.</span></span> <span data-ttu-id="1f855-215">Mit dem Zeitlimit wird der Code vor dem Verlust der Netzwerkkonnektivität oder vor JavaScript-Code geschützt, der keine Abschlussmeldung sendet.</span><span class="sxs-lookup"><span data-stu-id="1f855-215">The timeout protects the code against a loss in network connectivity or JavaScript code that never sends back a completion message.</span></span> <span data-ttu-id="1f855-216">Wenn beim Aufruf ein Timeout auftritt, schlägt die resultierende <xref:System.Threading.Tasks> mit einer <xref:System.OperationCanceledException>fehl.</span><span class="sxs-lookup"><span data-stu-id="1f855-216">If the call times out, the resulting <xref:System.Threading.Tasks> fails with an <xref:System.OperationCanceledException>.</span></span> <span data-ttu-id="1f855-217">Die Ausnahme wird abgefangen und mit Protokollierung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="1f855-217">Trap and process the exception with logging.</span></span>

<span data-ttu-id="1f855-218">Ähnlich kann JavaScript-Code Aufrufe von .NET-Methoden initiieren, die durch das [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript)-Attribut angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="1f855-218">Similarly, JavaScript code may initiate calls to .NET methods indicated by the [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) attribute.</span></span> <span data-ttu-id="1f855-219">Wenn diese .NET-Methoden einen Ausnahmefehler auslösen:</span><span class="sxs-lookup"><span data-stu-id="1f855-219">If these .NET methods throw an unhandled exception:</span></span>

* <span data-ttu-id="1f855-220">wird die Ausnahme nicht als schwerwiegend für eine Blazor Server-Leitung behandelt.</span><span class="sxs-lookup"><span data-stu-id="1f855-220">The exception isn't treated as fatal to a Blazor Server circuit.</span></span>
* <span data-ttu-id="1f855-221">wird die JavaScript-seitige `Promise` abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="1f855-221">The JavaScript-side `Promise` is rejected.</span></span>

<span data-ttu-id="1f855-222">Sie können Fehlerbehandlungscode auf der .NET-Seite oder auf der JavaScript-Seite des Methodenaufrufs verwenden.</span><span class="sxs-lookup"><span data-stu-id="1f855-222">You have the option of using error handling code on either the .NET side or the JavaScript side of the method call.</span></span>

<span data-ttu-id="1f855-223">Weitere Informationen finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="1f855-223">For more information, see the following articles:</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="no-locblazor-server-prerendering"></a><span data-ttu-id="1f855-224">Blazor Server-Prerendering</span><span class="sxs-lookup"><span data-stu-id="1f855-224">Blazor Server prerendering</span></span>

<span data-ttu-id="1f855-225">Blazor-Komponenten können mit dem [Komponententaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) vorab gerendert werden, sodass das gerenderte HTML-Markup als Teil der ursprünglichen HTTP-Anforderung des Benutzers zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="1f855-225">Blazor components can be prerendered using the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so that their rendered HTML markup is returned as part of the user's initial HTTP request.</span></span> <span data-ttu-id="1f855-226">Dies funktioniert durch:</span><span class="sxs-lookup"><span data-stu-id="1f855-226">This works by:</span></span>

* <span data-ttu-id="1f855-227">Erstellen einer neuen Leitung für alle vorab gerenderten Komponenten, die Teil derselben Seite sind</span><span class="sxs-lookup"><span data-stu-id="1f855-227">Creating a new circuit for all of the prerendered components that are part of the same page.</span></span>
* <span data-ttu-id="1f855-228">Generieren des ursprünglichen HTML-Codes</span><span class="sxs-lookup"><span data-stu-id="1f855-228">Generating the initial HTML.</span></span>
* <span data-ttu-id="1f855-229">Behandeln der Leitung als `disconnected`, bis der Browser des Benutzers eine SignalR-Verbindung zurück zum selben Server einrichtet.</span><span class="sxs-lookup"><span data-stu-id="1f855-229">Treating the circuit as `disconnected` until the user's browser establishes a SignalR connection back to the same server.</span></span> <span data-ttu-id="1f855-230">Wenn die Verbindung hergestellt wird, wird die Interaktivität in der Leitung fortgesetzt und das HTML-Markup der Komponenten wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="1f855-230">When the connection is established, interactivity on the circuit is resumed and the components' HTML markup is updated.</span></span>

<span data-ttu-id="1f855-231">Wenn von einer Komponente während des Prerenderings ein Ausnahmefehler beispielswiese während einer Lebenszyklusmethode oder in der Renderinglogik ausgelöst wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1f855-231">If any component throws an unhandled exception during prerendering, for example, during a lifecycle method or in rendering logic:</span></span>

* <span data-ttu-id="1f855-232">Die Ausnahme ist für die Leitung schwerwiegend.</span><span class="sxs-lookup"><span data-stu-id="1f855-232">The exception is fatal to the circuit.</span></span>
* <span data-ttu-id="1f855-233">Die Ausnahme wird vom <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>-Taghilfsprogramm in der Aufrufliste weiter oben ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="1f855-233">The exception is thrown up the call stack from the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span> <span data-ttu-id="1f855-234">Daher schlägt die gesamte HTTP-Anforderung fehl, es sei denn, die Ausnahme wird explizit vom Entwicklercode abgefangen.</span><span class="sxs-lookup"><span data-stu-id="1f855-234">Therefore, the entire HTTP request fails unless the exception is explicitly caught by developer code.</span></span>

<span data-ttu-id="1f855-235">Wenn das Prerendering unter normalen Umständen fehlschlägt, ist es nicht sinnvoll, mit dem Erstellen und Rendern der Komponente fortzufahren, da eine funktionierende Komponente nicht gerendert werden kann.</span><span class="sxs-lookup"><span data-stu-id="1f855-235">Under normal circumstances when prerendering fails, continuing to build and render the component doesn't make sense because a working component can't be rendered.</span></span>

<span data-ttu-id="1f855-236">Wenn während des Prerenderings auftretende Fehler toleriert werden sollen, muss sich die Fehlerbehandlungslogik in einer Komponente befinden, die möglicherweise Ausnahmen auslöst.</span><span class="sxs-lookup"><span data-stu-id="1f855-236">To tolerate errors that may occur during prerendering, error handling logic must be placed inside a component that may throw exceptions.</span></span> <span data-ttu-id="1f855-237">Verwenden Sie [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisungen mit Fehlerbehandlung und Fehlerprotokollierung.</span><span class="sxs-lookup"><span data-stu-id="1f855-237">Use [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements with error handling and logging.</span></span> <span data-ttu-id="1f855-238">Statt das <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>-Tag-Hilfsprogramm in einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung zu umschließen, fügen Sie eine Fehlerbehandlungslogik in die vom <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>-Tag-Hilfsprogramm gerenderte Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="1f855-238">Instead of wrapping the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement, place error handling logic in the component rendered by the <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> Tag Helper.</span></span>

## <a name="advanced-scenarios"></a><span data-ttu-id="1f855-239">Erweiterte Szenarien</span><span class="sxs-lookup"><span data-stu-id="1f855-239">Advanced scenarios</span></span>

### <a name="recursive-rendering"></a><span data-ttu-id="1f855-240">Rekursives Rendering</span><span class="sxs-lookup"><span data-stu-id="1f855-240">Recursive rendering</span></span>

<span data-ttu-id="1f855-241">Komponenten können rekursiv geschachtelt sein.</span><span class="sxs-lookup"><span data-stu-id="1f855-241">Components can be nested recursively.</span></span> <span data-ttu-id="1f855-242">Dies ist nützlich, um rekursive Datenstrukturen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="1f855-242">This is useful for representing recursive data structures.</span></span> <span data-ttu-id="1f855-243">So kann eine `TreeNode`-Komponente beispielsweise weitere `TreeNode`-Komponenten für die einzelnen untergeordneten Elemente des Knotens rendern.</span><span class="sxs-lookup"><span data-stu-id="1f855-243">For example, a `TreeNode` component can render more `TreeNode` components for each of the node's children.</span></span>

<span data-ttu-id="1f855-244">Vermeiden Sie beim rekursiven Rendern Codemuster, die zu einer Endlosschleife führen:</span><span class="sxs-lookup"><span data-stu-id="1f855-244">When rendering recursively, avoid coding patterns that result in infinite recursion:</span></span>

* <span data-ttu-id="1f855-245">Vermeiden Sie rekursives Rendering bei einer Datenstruktur, die einen Zyklus enthält.</span><span class="sxs-lookup"><span data-stu-id="1f855-245">Don't recursively render a data structure that contains a cycle.</span></span> <span data-ttu-id="1f855-246">Rendern Sie beispielsweise keinen Strukturknoten, dessen untergeordnete Knoten sich selbst enthalten.</span><span class="sxs-lookup"><span data-stu-id="1f855-246">For example, don't render a tree node whose children includes itself.</span></span>
* <span data-ttu-id="1f855-247">Erstellen Sie keine Layoutkette, die einen Zyklus enthält.</span><span class="sxs-lookup"><span data-stu-id="1f855-247">Don't create a chain of layouts that contain a cycle.</span></span> <span data-ttu-id="1f855-248">Erstellen Sie beispielsweise kein Layout, das sein eigenes Layout ist.</span><span class="sxs-lookup"><span data-stu-id="1f855-248">For example, don't create a layout whose layout is itself.</span></span>
* <span data-ttu-id="1f855-249">Lassen Sie nicht zu, dass ein Endbenutzer Rekursionsinvarianten (Regeln) durch eine böswillige Dateneingabe oder JavaScript-Interoperabilitätsaufrufe verletzt.</span><span class="sxs-lookup"><span data-stu-id="1f855-249">Don't allow an end user to violate recursion invariants (rules) through malicious data entry or JavaScript interop calls.</span></span>

<span data-ttu-id="1f855-250">Endlosschleifen durch Rendering:</span><span class="sxs-lookup"><span data-stu-id="1f855-250">Infinite loops during rendering:</span></span>

* <span data-ttu-id="1f855-251">sorgt dafür, dass der Renderingprozess unendlich fortgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="1f855-251">Causes the rendering process to continue forever.</span></span>
* <span data-ttu-id="1f855-252">entspricht dem Erstellen einer nicht abgeschlossenen Schleife.</span><span class="sxs-lookup"><span data-stu-id="1f855-252">Is equivalent to creating an unterminated loop.</span></span>

<span data-ttu-id="1f855-253">In diesen Szenarios tritt ein Fehler bei einer betroffenen Blazor Server-Leitung auf, und der Thread versucht in der Regel Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1f855-253">In these scenarios, an affected Blazor Server circuit fails, and the thread usually attempts to:</span></span>

* <span data-ttu-id="1f855-254">Unbegrenzt so viel CPU-Zeit wie vom Betriebssystem zulässig zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="1f855-254">Consume as much CPU time as permitted by the operating system, indefinitely.</span></span>
* <span data-ttu-id="1f855-255">Eine unbegrenzte Menge an Serverarbeitsspeicher zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="1f855-255">Consume an unlimited amount of server memory.</span></span> <span data-ttu-id="1f855-256">Das Belegen einer unbegrenzte Menge an Arbeitsspeicher entspricht einem Szenario, bei dem eine nicht beendete Schleife einer Auflistung bei jeder Iteration Einträge hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="1f855-256">Consuming unlimited memory is equivalent to the scenario where an unterminated loop adds entries to a collection on every iteration.</span></span>

<span data-ttu-id="1f855-257">Wenn Sie Muster mit Endlosrekursionen vermeiden möchten, müssen Sie sicherstellen, dass der Renderingcode geeignete Beendigungsbedingungen enthält.</span><span class="sxs-lookup"><span data-stu-id="1f855-257">To avoid infinite recursion patterns, ensure that recursive rendering code contains suitable stopping conditions.</span></span>

### <a name="custom-render-tree-logic"></a><span data-ttu-id="1f855-258">Benutzerdefinierte Renderstrukturlogik</span><span class="sxs-lookup"><span data-stu-id="1f855-258">Custom render tree logic</span></span>

<span data-ttu-id="1f855-259">Die meisten Blazor-Komponenten werden als `.razor`-Dateien implementiert und kompiliert, um eine Logik zu erzeugen, die einen <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> verwendet, um dessen Ausgabe zu rendern.</span><span class="sxs-lookup"><span data-stu-id="1f855-259">Most Blazor components are implemented as `.razor` files and are compiled to produce logic that operates on a <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> to render their output.</span></span> <span data-ttu-id="1f855-260">Ein Entwickler kann mit prozeduralem C#-Code eine <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Logik manuell implementieren.</span><span class="sxs-lookup"><span data-stu-id="1f855-260">A developer may manually implement <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> logic using procedural C# code.</span></span> <span data-ttu-id="1f855-261">Weitere Informationen finden Sie unter <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span><span class="sxs-lookup"><span data-stu-id="1f855-261">For more information, see <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.</span></span>

> [!WARNING]
> <span data-ttu-id="1f855-262">Die Verwendung einer manuellen Buildlogik für die Renderstruktur gilt als erweitertes und unsicheres Szenario, das für die allgemeine Komponentenentwicklung nicht empfohlen wird.</span><span class="sxs-lookup"><span data-stu-id="1f855-262">Use of manual render tree builder logic is considered an advanced and unsafe scenario, not recommended for general component development.</span></span>

<span data-ttu-id="1f855-263">Beim Schreiben von <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Code muss der Entwickler die Richtigkeit des Codes garantieren.</span><span class="sxs-lookup"><span data-stu-id="1f855-263">If <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> code is written, the developer must guarantee the correctness of the code.</span></span> <span data-ttu-id="1f855-264">Der Entwickler muss beispielsweise Folgendes sicherstellen:</span><span class="sxs-lookup"><span data-stu-id="1f855-264">For example, the developer must ensure that:</span></span>

* <span data-ttu-id="1f855-265">Aufrufe von <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> und <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> sind ordnungsgemäß ausgeglichen.</span><span class="sxs-lookup"><span data-stu-id="1f855-265">Calls to <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> and <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> are correctly balanced.</span></span>
* <span data-ttu-id="1f855-266">Attribute werden nur an den richtigen Stellen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1f855-266">Attributes are only added in the correct places.</span></span>

<span data-ttu-id="1f855-267">Eine fehlerhafte Buildlogik für die Renderstruktur kann ein willkürliches undefiniertes Verhalten wie Abstürze, Serverabstürze und Sicherheitsrisiken verursachen.</span><span class="sxs-lookup"><span data-stu-id="1f855-267">Incorrect manual render tree builder logic can cause arbitrary undefined behavior, including crashes, server hangs, and security vulnerabilities.</span></span>

<span data-ttu-id="1f855-268">Eine manuelle Buildlogik für die Renderstruktur ist genauso komplex und birgt dasselbe Maß an *Gefahren* wie das manuelle Schreiben von Assemblycode oder MSIL-Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="1f855-268">Consider manual render tree builder logic on the same level of complexity and with the same level of *danger* as writing assembly code or MSIL instructions by hand.</span></span>
