---
title: Einführung in ASP.NET Core Blazor
author: guardrex
description: Lernen Sie ASP.NET Core Blazor kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
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
uid: blazor/index
ms.openlocfilehash: bd42ba41c6f599d7c0308febbbde71e0d060cc84
ms.sourcegitcommit: 20a41c8e40a2e69e99291e2fe18caa04c02e7109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99578294"
---
# <a name="introduction-to-aspnet-core-blazor"></a><span data-ttu-id="10af3-103">Einführung in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="10af3-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="10af3-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="10af3-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="10af3-105">*Willkommen bei Blazor!*</span><span class="sxs-lookup"><span data-stu-id="10af3-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="10af3-106">Blazor ist ein Framework zum Erstellen einer interaktiven clientseitigen Webbenutzeroberfläche mit [.NET](/dotnet/standard/tour):</span><span class="sxs-lookup"><span data-stu-id="10af3-106">Blazor is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="10af3-107">Erstellen Sie umfassende interaktive Benutzeroberflächen mit [C#](/dotnet/csharp/) anstatt mit [JavaScript](https://www.javascript.com).</span><span class="sxs-lookup"><span data-stu-id="10af3-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="10af3-108">Gemeinsames Verwenden von server- und clientseitiger App-Logik, die in .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="10af3-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="10af3-109">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="10af3-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="10af3-110">Integrieren mit modernen Hostingplattformen, z. B. [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)</span><span class="sxs-lookup"><span data-stu-id="10af3-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="10af3-111">Die Verwendung von .NET im für die clientseitige Webentwicklung bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="10af3-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="10af3-112">Schreiben Sie Code in C# anstatt in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="10af3-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="10af3-113">Nutzen Sie das vorhandene .NET-Ökosystem von [.NET-Bibliotheken](/dotnet/standard/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="10af3-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="10af3-114">Geben Sie die App-Logik server- und clientübergreifend frei.</span><span class="sxs-lookup"><span data-stu-id="10af3-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="10af3-115">Profitieren Sie von Leistung, Zuverlässigkeit und Sicherheit von .NET.</span><span class="sxs-lookup"><span data-stu-id="10af3-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="10af3-116">Bleiben Sie mit [Visual Studio](https://visualstudio.microsoft.com) unter Windows, Linux und macOS produktiv.</span><span class="sxs-lookup"><span data-stu-id="10af3-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="10af3-117">Setzen Sie auf gebräuchliche Sprachen, Frameworks und Tools, die stabil, funktionsreich und einfach zu verwenden sind.</span><span class="sxs-lookup"><span data-stu-id="10af3-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="10af3-118">Komponenten</span><span class="sxs-lookup"><span data-stu-id="10af3-118">Components</span></span>

<span data-ttu-id="10af3-119">Blazor-Apps basieren auf *Komponenten*.</span><span class="sxs-lookup"><span data-stu-id="10af3-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="10af3-120">Eine Komponente in Blazor ist ein Element der Benutzeroberfläche, beispielsweise eine Seite, ein Dialogfeld oder ein Formular für die Dateneingabe.</span><span class="sxs-lookup"><span data-stu-id="10af3-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="10af3-121">Komponenten sind in [.NET-Assemblys](/dotnet/standard/assembly/) integrierte .NET-C#-Klassen, auf die Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="10af3-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="10af3-122">Sie definieren die Logik für ein flexibles Rendering der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="10af3-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="10af3-123">Sie behandeln Benutzerereignisse.</span><span class="sxs-lookup"><span data-stu-id="10af3-123">Handle user events.</span></span>
* <span data-ttu-id="10af3-124">Sie können geschachtelt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="10af3-124">Can be nested and reused.</span></span>
* <span data-ttu-id="10af3-125">Sie können als [Razor-Klassenbibliotheken](xref:razor-pages/ui-class) oder [NuGet-Pakete](/nuget/what-is-nuget) freigegeben und verteilt werden.</span><span class="sxs-lookup"><span data-stu-id="10af3-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="10af3-126">Die Komponentenklasse wird in der Regel in Form einer [Razor](xref:mvc/views/razor)-Markupseite mit der Dateierweiterung `.razor` geschrieben.</span><span class="sxs-lookup"><span data-stu-id="10af3-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="10af3-127">Komponenten in Blazor werden formal als *Razor-Komponenten* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="10af3-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="10af3-128">Razor ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann, ausgerichtet auf die Produktivität der Entwickler.</span><span class="sxs-lookup"><span data-stu-id="10af3-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="10af3-129">Razor ermöglicht es Ihnen, mit [IntelliSense](/visualstudio/ide/using-intellisense)-Programmierunterstützung in Visual Studio in ein und derselben Datei zwischen HTML-Markup und C# zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="10af3-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="10af3-130">Razor Pages und MVC verwenden ebenfalls Razor.</span><span class="sxs-lookup"><span data-stu-id="10af3-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="10af3-131">Im Gegensatz zu Razor Pages und MVC, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="10af3-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="10af3-132">Blazor verwendet natürliche HTML-Tags für die Benutzeroberflächengestaltung.</span><span class="sxs-lookup"><span data-stu-id="10af3-132">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="10af3-133">Das folgende Razor-Markup veranschaulicht eine Komponente (`Dialog.razor`), die ein Dialogfeld anzeigt und ein Ereignis verarbeitet, wenn der Benutzer eine Schaltfläche auswählt:</span><span class="sxs-lookup"><span data-stu-id="10af3-133">The following Razor markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="10af3-134">Im obigen Beispiel ist `OnYes` eine C#-Methode, die durch das Schaltflächenereignis `onclick` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="10af3-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="10af3-135">Der Text (`ChildContent`) und der Titel (`Title`) des Dialogfelds werden von der folgenden Komponente bereitgestellt, die diese Komponente in ihrer Benutzeroberfläche verwendet.</span><span class="sxs-lookup"><span data-stu-id="10af3-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="10af3-136">Die Komponente `Dialog` ist mithilfe eines HTML-Tags in einer anderen Komponente geschachtelt.</span><span class="sxs-lookup"><span data-stu-id="10af3-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="10af3-137">Im folgenden Beispiel verwendet die `Index`-Komponente (`Pages/Index.razor`) die vorherige `Dialog`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="10af3-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="10af3-138">Das `Title`-Attribut des Tags übergibt einen Wert für den Titel an die `Dialog`-Eigenschaft der `Title`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="10af3-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="10af3-139">Der Text der `Dialog`-Komponente (`ChildContent`) wird durch den Inhalt des `<Dialog>`-Elements festgelegt.</span><span class="sxs-lookup"><span data-stu-id="10af3-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="10af3-140">Wenn die `Dialog`-Komponente der `Index`-Komponente hinzugefügt wird, beschleunigt [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) die Entwicklung mithilfe von Syntax- und Parametervervollständigung.</span><span class="sxs-lookup"><span data-stu-id="10af3-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="10af3-141">Das Dialogfeld wird gerendert, wenn die `Index`-Komponente in einem Browser aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="10af3-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="10af3-142">Wenn ein Benutzer die Schaltfläche auswählt, zeigt die Browserkonsole mit den Entwicklertools die von der `OnYes`-Methode geschriebene Meldung:</span><span class="sxs-lookup"><span data-stu-id="10af3-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![Dialogfeldkomponente, die im Browser geschachtelt in der Indexkomponente gerendert wird.](index/_static/dialog.png)

<span data-ttu-id="10af3-146">Die Komponenten werden in einer In-Memory-Darstellung des [Dokumentobjektmodells](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) (DOM) des Browsers gerendert, die als *Renderstruktur* bezeichnet wird. Mit dieser Struktur lässt sich die Benutzeroberfläche flexibel und effizient aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="10af3-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="10af3-147">Blazor WebAssembly ist ein [Single-Page-App-Framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) zum Erstellen von interaktiven clientseitigen Web-Apps mit .NET.</span><span class="sxs-lookup"><span data-stu-id="10af3-147">Blazor WebAssembly is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="10af3-148">Blazor WebAssembly verwendet offene Webstandards ohne Plug-Ins und ohne dass Code in anderen Sprachen neu kompiliert werden muss.</span><span class="sxs-lookup"><span data-stu-id="10af3-148">Blazor WebAssembly uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="10af3-149">Blazor WebAssembly funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.</span><span class="sxs-lookup"><span data-stu-id="10af3-149">Blazor WebAssembly works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="10af3-150">Das Ausführen von .NET-Code in einem Webbrowser wird durch [WebAssembly](https://webassembly.org) (Kurzform: `wasm`) ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="10af3-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="10af3-151">Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist.</span><span class="sxs-lookup"><span data-stu-id="10af3-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="10af3-152">WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="10af3-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="10af3-153">WebAssembly-Code kann über JavaScript auf alle Funktionen des Browsers zugreifen. Dies wird als *JavaScript-Interoperabilität* bezeichnet (häufig als *JavaScript Interop* oder *JS Interop* abgekürzt).</span><span class="sxs-lookup"><span data-stu-id="10af3-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability*, often shortened to *JavaScript interop* or *JS interop*.</span></span> <span data-ttu-id="10af3-154">Über WebAssembly im Browser ausgeführter .NET-Code wird in der JavaScript-Sandbox des Browsers ausgeführt. Hierbei greifen die Schutzmaßnahmen der Sandbox gegen schädliche Aktionen auf dem Clientcomputer.</span><span class="sxs-lookup"><span data-stu-id="10af3-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![Blazor WebAssembly führt .NET-Code unter Verwendung von WebAssembly im Browser aus.](index/_static/blazor-webassembly.png)

<span data-ttu-id="10af3-156">Folgendes geschieht, wenn eine Blazor WebAssembly-App in einem Browser erstellt und ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="10af3-156">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="10af3-157">C#-Codedateien und Razor-Dateien werden in .NET-Assemblys kompiliert.</span><span class="sxs-lookup"><span data-stu-id="10af3-157">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="10af3-158">Die Assemblys und die [.NET-Runtime](/dotnet/framework/get-started/overview) werden in den Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="10af3-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="10af3-159">Blazor WebAssembly startet die .NET-Runtime und konfiguriert die Runtime zum Laden der Assemblys für die App.</span><span class="sxs-lookup"><span data-stu-id="10af3-159">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="10af3-160">DOM-Änderungen und API-Aufrufe im Browser werden von der Blazor WebAssembly-Runtime über die JavaScript-Interop verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="10af3-160">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="10af3-161">Die Größe der veröffentlichten App, ihre *Nutzlastgröße*, ist ein wichtiger Leistungsfaktor für die Verwendbarkeit einer App.</span><span class="sxs-lookup"><span data-stu-id="10af3-161">The size of the published app, its *payload size*, is a critical performance factor for an app's usability.</span></span> <span data-ttu-id="10af3-162">Das Herunterladen einer großen App in einen Browser dauert relativ lange, was die Benutzerfreundlichkeit beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="10af3-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="10af3-163">Blazor WebAssembly optimiert die Nutzlastgröße, um die Downloadzeiten zu verringern:</span><span class="sxs-lookup"><span data-stu-id="10af3-163">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="10af3-164">Nicht verwendeter Code wird aus der App entfernt, wenn sie vom [IL-Trimmer (Intermediate Language)](xref:blazor/host-and-deploy/configure-trimmer) veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="10af3-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="10af3-165">HTTP-Antworten werden komprimiert.</span><span class="sxs-lookup"><span data-stu-id="10af3-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="10af3-166">Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="10af3-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="10af3-167">Nicht verwendeter Code wird aus der App entfernt, wenn sie vom [IL-Linker (Intermediate Language)](xref:blazor/host-and-deploy/configure-linker) veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="10af3-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="10af3-168">HTTP-Antworten werden komprimiert.</span><span class="sxs-lookup"><span data-stu-id="10af3-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="10af3-169">Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="10af3-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="10af3-170">Blazor entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="10af3-170">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="10af3-171">*Blazor Server* bietet Unterstützung für das Hosten von Razor-Komponenten in einer ASP.NET Core-App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="10af3-171">*Blazor Server* provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="10af3-172">Aktualisierungen der Benutzeroberfläche werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="10af3-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="10af3-173">Die Runtime verarbeitet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="10af3-173">The runtime handles:</span></span>

* <span data-ttu-id="10af3-174">Senden von Benutzeroberflächenereignissen vom Browser an den Server.</span><span class="sxs-lookup"><span data-stu-id="10af3-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="10af3-175">Anwenden von Benutzeroberflächenupdates, die vom Server zurückgesendet werden, auf die gerenderte Komponente.</span><span class="sxs-lookup"><span data-stu-id="10af3-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="10af3-176">Die Verbindung, die von Blazor Server für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.</span><span class="sxs-lookup"><span data-stu-id="10af3-176">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Blazor Server führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung mit dem Dokumentobjektmodell.](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="10af3-178">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="10af3-178">JavaScript interop</span></span>

<span data-ttu-id="10af3-179">Für Apps, die JavaScript-Bibliotheken und Zugriff auf Browser-APIs von Drittanbietern benötigen, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert.</span><span class="sxs-lookup"><span data-stu-id="10af3-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="10af3-180">Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="10af3-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="10af3-181">C#-Code kann [JavaScript-Code abfragen](xref:blazor/call-javascript-from-dotnet), und JavaScript-Code kann [C#-Code abfragen](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="10af3-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="10af3-182">Codefreigabe und .NET Standard</span><span class="sxs-lookup"><span data-stu-id="10af3-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="10af3-183">Blazor implementiert den [.NET Standard](/dotnet/standard/net-standard), über den Blazor-Projekte auf Bibliotheken verweisen können, die den .NET Standard-Spezifikationen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="10af3-183">Blazor implements the [.NET Standard](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="10af3-184">.NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="10af3-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="10af3-185">.NET-Standardklassenbibliotheken können auf verschiedenen .NET-Plattformen wie Blazor, .NET Framework, .NET Core, Xamarin, Mono und Unity freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="10af3-185">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="10af3-186">APIs, die nicht in einem Webbrowser angewendet werden können (z.B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets und für das Threading), lösen die Ausnahme <xref:System.PlatformNotSupportedException> aus.</span><span class="sxs-lookup"><span data-stu-id="10af3-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10af3-187">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="10af3-187">Additional resources</span></span>

* [<span data-ttu-id="10af3-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="10af3-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="10af3-189">Leitfaden für C#</span><span class="sxs-lookup"><span data-stu-id="10af3-189">C# Guide</span></span>](/dotnet/csharp/)
* [<span data-ttu-id="10af3-190">Razor-Syntaxreferenz für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="10af3-190">Razor syntax reference for ASP.NET Core</span></span>](xref:mvc/views/razor)
* [<span data-ttu-id="10af3-191">HTML</span><span class="sxs-lookup"><span data-stu-id="10af3-191">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="10af3-192">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) Community-Links</span><span class="sxs-lookup"><span data-stu-id="10af3-192">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
