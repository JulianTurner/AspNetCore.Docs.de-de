---
title: 'Einführung in ASP.NET Core :::no-loc(Blazor):::'
author: guardrex
description: 'Lernen Sie ASP.NET Core :::no-loc(Blazor)::: kennen, eine Möglichkeit, interaktive clientseitige Webbenutzeroberflächen mit .NET in einer ASP.NET Core-App zu erstellen.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019, devx-track-js
ms.date: 09/25/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/index
ms.openlocfilehash: ecdf4f59aca0fe71bbfcfe61a99109127c8b92df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055711"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="520c4-103">Einführung in ASP.NET Core :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="520c4-103">Introduction to ASP.NET Core :::no-loc(Blazor):::</span></span>

<span data-ttu-id="520c4-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="520c4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="520c4-105">*Willkommen bei :::no-loc(Blazor):::!*</span><span class="sxs-lookup"><span data-stu-id="520c4-105">*Welcome to :::no-loc(Blazor):::!*</span></span>

<span data-ttu-id="520c4-106">:::no-loc(Blazor)::: ist ein Framework zum Erstellen einer interaktiven clientseitigen Webbenutzeroberfläche mit [.NET](/dotnet/standard/tour):</span><span class="sxs-lookup"><span data-stu-id="520c4-106">:::no-loc(Blazor)::: is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="520c4-107">Erstellen Sie umfassende interaktive Benutzeroberflächen mit [C#](/dotnet/csharp/) anstatt mit [JavaScript](https://www.javascript.com).</span><span class="sxs-lookup"><span data-stu-id="520c4-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="520c4-108">Gemeinsames Verwenden von server- und clientseitiger App-Logik, die in .NET geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="520c4-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="520c4-109">Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).</span><span class="sxs-lookup"><span data-stu-id="520c4-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="520c4-110">Integrieren mit modernen Hostingplattformen, z. B. [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)</span><span class="sxs-lookup"><span data-stu-id="520c4-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="520c4-111">Die Verwendung von .NET im für die clientseitige Webentwicklung bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="520c4-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="520c4-112">Schreiben Sie Code in C# anstatt in JavaScript.</span><span class="sxs-lookup"><span data-stu-id="520c4-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="520c4-113">Nutzen Sie das vorhandene .NET-Ökosystem von [.NET-Bibliotheken](/dotnet/standard/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="520c4-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="520c4-114">Geben Sie die App-Logik server- und clientübergreifend frei.</span><span class="sxs-lookup"><span data-stu-id="520c4-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="520c4-115">Profitieren Sie von Leistung, Zuverlässigkeit und Sicherheit von .NET.</span><span class="sxs-lookup"><span data-stu-id="520c4-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="520c4-116">Bleiben Sie mit [Visual Studio](https://visualstudio.microsoft.com) unter Windows, Linux und macOS produktiv.</span><span class="sxs-lookup"><span data-stu-id="520c4-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="520c4-117">Setzen Sie auf gebräuchliche Sprachen, Frameworks und Tools, die stabil, funktionsreich und einfach zu verwenden sind.</span><span class="sxs-lookup"><span data-stu-id="520c4-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="520c4-118">Komponenten</span><span class="sxs-lookup"><span data-stu-id="520c4-118">Components</span></span>

<span data-ttu-id="520c4-119">:::no-loc(Blazor):::-Apps basieren auf *Komponenten* .</span><span class="sxs-lookup"><span data-stu-id="520c4-119">:::no-loc(Blazor)::: apps are based on *components* .</span></span> <span data-ttu-id="520c4-120">Eine Komponente in :::no-loc(Blazor)::: ist ein Element der Benutzeroberfläche, beispielsweise eine Seite, ein Dialogfeld oder ein Formular für die Dateneingabe.</span><span class="sxs-lookup"><span data-stu-id="520c4-120">A component in :::no-loc(Blazor)::: is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="520c4-121">Komponenten sind in [.NET-Assemblys](/dotnet/standard/assembly/) integrierte .NET-C#-Klassen, auf die Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="520c4-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="520c4-122">Sie definieren die Logik für ein flexibles Rendering der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="520c4-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="520c4-123">Sie behandeln Benutzerereignisse.</span><span class="sxs-lookup"><span data-stu-id="520c4-123">Handle user events.</span></span>
* <span data-ttu-id="520c4-124">Sie können geschachtelt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="520c4-124">Can be nested and reused.</span></span>
* <span data-ttu-id="520c4-125">Sie können als [:::no-loc(Razor):::-Klassenbibliotheken](xref:razor-pages/ui-class) oder [NuGet-Pakete](/nuget/what-is-nuget) freigegeben und verteilt werden.</span><span class="sxs-lookup"><span data-stu-id="520c4-125">Can be shared and distributed as [:::no-loc(Razor)::: class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="520c4-126">Die Komponentenklasse wird in der Regel in Form einer [:::no-loc(Razor):::](xref:mvc/views/razor)-Markupseite mit der Dateierweiterung `.razor` geschrieben.</span><span class="sxs-lookup"><span data-stu-id="520c4-126">The component class is usually written in the form of a [:::no-loc(Razor):::](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="520c4-127">Komponenten in :::no-loc(Blazor)::: werden formal als *:::no-loc(Razor):::-Komponenten* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="520c4-127">Components in :::no-loc(Blazor)::: are formally referred to as *:::no-loc(Razor)::: components* .</span></span> <span data-ttu-id="520c4-128">:::no-loc(Razor)::: ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann, ausgerichtet auf die Produktivität der Entwickler.</span><span class="sxs-lookup"><span data-stu-id="520c4-128">:::no-loc(Razor)::: is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="520c4-129">:::no-loc(Razor)::: ermöglicht es Ihnen, mit [IntelliSense](/visualstudio/ide/using-intellisense)-Programmierunterstützung in Visual Studio in ein und derselben Datei zwischen HTML-Markup und C# zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="520c4-129">:::no-loc(Razor)::: allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="520c4-130">:::no-loc(Razor)::: Pages und MVC verwenden ebenfalls :::no-loc(Razor):::.</span><span class="sxs-lookup"><span data-stu-id="520c4-130">:::no-loc(Razor)::: Pages and MVC also use :::no-loc(Razor):::.</span></span> <span data-ttu-id="520c4-131">Im Gegensatz zu :::no-loc(Razor)::: Pages und MVC, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet.</span><span class="sxs-lookup"><span data-stu-id="520c4-131">Unlike :::no-loc(Razor)::: Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="520c4-132">:::no-loc(Blazor)::: verwendet natürliche HTML-Tags für die Benutzeroberflächengestaltung.</span><span class="sxs-lookup"><span data-stu-id="520c4-132">:::no-loc(Blazor)::: uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="520c4-133">Das folgende :::no-loc(Razor):::-Markup veranschaulicht eine Komponente (`Dialog.razor`), die ein Dialogfeld anzeigt und ein Ereignis verarbeitet, wenn der Benutzer eine Schaltfläche auswählt:</span><span class="sxs-lookup"><span data-stu-id="520c4-133">The following :::no-loc(Razor)::: markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

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

<span data-ttu-id="520c4-134">Im obigen Beispiel ist `OnYes` eine C#-Methode, die durch das Schaltflächenereignis `onclick` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="520c4-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="520c4-135">Der Text (`ChildContent`) und der Titel (`Title`) des Dialogfelds werden von der folgenden Komponente bereitgestellt, die diese Komponente in ihrer Benutzeroberfläche verwendet.</span><span class="sxs-lookup"><span data-stu-id="520c4-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="520c4-136">Die Komponente `Dialog` ist mithilfe eines HTML-Tags in einer anderen Komponente geschachtelt.</span><span class="sxs-lookup"><span data-stu-id="520c4-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="520c4-137">Im folgenden Beispiel verwendet die `Index`-Komponente (`Pages/Index.razor`) die vorherige `Dialog`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="520c4-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="520c4-138">Das `Title`-Attribut des Tags übergibt einen Wert für den Titel an die `Dialog`-Eigenschaft der `Title`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="520c4-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="520c4-139">Der Text der `Dialog`-Komponente (`ChildContent`) wird durch den Inhalt des `<Dialog>`-Elements festgelegt.</span><span class="sxs-lookup"><span data-stu-id="520c4-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="520c4-140">Wenn die `Dialog`-Komponente der `Index`-Komponente hinzugefügt wird, beschleunigt [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) die Entwicklung mithilfe von Syntax- und Parametervervollständigung.</span><span class="sxs-lookup"><span data-stu-id="520c4-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about :::no-loc(Blazor):::?
</Dialog>
```

<span data-ttu-id="520c4-141">Das Dialogfeld wird gerendert, wenn die `Index`-Komponente in einem Browser aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="520c4-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="520c4-142">Wenn ein Benutzer die Schaltfläche auswählt, zeigt die Browserkonsole mit den Entwicklertools die von der `OnYes`-Methode geschriebene Meldung:</span><span class="sxs-lookup"><span data-stu-id="520c4-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![Dialogfeldkomponente, die im Browser geschachtelt in der Indexkomponente gerendert wird.](index/_static/dialog.png)

<span data-ttu-id="520c4-146">Die Komponenten werden in einer In-Memory-Darstellung des [Dokumentobjektmodells](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) (DOM) des Browsers gerendert, die als *Renderstruktur* bezeichnet wird. Mit dieser Struktur lässt sich die Benutzeroberfläche flexibel und effizient aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="520c4-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree* , which is used to update the UI in a flexible and efficient way.</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="520c4-147">:::no-loc(Blazor WebAssembly)::: ist ein [Single-Page-App-Framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) zum Erstellen von interaktiven clientseitigen Web-Apps mit .NET.</span><span class="sxs-lookup"><span data-stu-id="520c4-147">:::no-loc(Blazor WebAssembly)::: is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="520c4-148">:::no-loc(Blazor WebAssembly)::: verwendet offene Webstandards ohne Plug-Ins und ohne dass Code in anderen Sprachen neu kompiliert werden muss.</span><span class="sxs-lookup"><span data-stu-id="520c4-148">:::no-loc(Blazor WebAssembly)::: uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="520c4-149">:::no-loc(Blazor WebAssembly)::: funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.</span><span class="sxs-lookup"><span data-stu-id="520c4-149">:::no-loc(Blazor WebAssembly)::: works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="520c4-150">Das Ausführen von .NET-Code in einem Webbrowser wird durch [WebAssembly](https://webassembly.org) (Kurzform: `wasm`) ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="520c4-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="520c4-151">Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist.</span><span class="sxs-lookup"><span data-stu-id="520c4-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="520c4-152">WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="520c4-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="520c4-153">WebAssembly-Code kann über JavaScript auf alle Funktionen des Browsers zugreifen. Dies wird als *JavaScript-Interoperabilität* bezeichnet (häufig als *JavaScript Interop* oder *JS Interop* abgekürzt).</span><span class="sxs-lookup"><span data-stu-id="520c4-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability* , often shortened to *JavaScript interop* or *JS interop* .</span></span> <span data-ttu-id="520c4-154">Über WebAssembly im Browser ausgeführter .NET-Code wird in der JavaScript-Sandbox des Browsers ausgeführt. Hierbei greifen die Schutzmaßnahmen der Sandbox gegen schädliche Aktionen auf dem Clientcomputer.</span><span class="sxs-lookup"><span data-stu-id="520c4-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![:::no-loc(Blazor WebAssembly)::: führt .NET-Code unter Verwendung von WebAssembly im Browser aus.](index/_static/blazor-webassembly.png)

<span data-ttu-id="520c4-156">Folgendes geschieht, wenn eine :::no-loc(Blazor WebAssembly):::-App in einem Browser erstellt und ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="520c4-156">When a :::no-loc(Blazor WebAssembly)::: app is built and run in a browser:</span></span>

* <span data-ttu-id="520c4-157">C#-Codedateien und :::no-loc(Razor):::-Dateien werden in .NET-Assemblys kompiliert.</span><span class="sxs-lookup"><span data-stu-id="520c4-157">C# code files and :::no-loc(Razor)::: files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="520c4-158">Die Assemblys und die [.NET-Runtime](/dotnet/framework/get-started/overview) werden in den Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="520c4-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="520c4-159">:::no-loc(Blazor WebAssembly)::: startet die .NET-Runtime und konfiguriert die Runtime zum Laden der Assemblys für die App.</span><span class="sxs-lookup"><span data-stu-id="520c4-159">:::no-loc(Blazor WebAssembly)::: bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="520c4-160">DOM-Änderungen und API-Aufrufe im Browser werden von der :::no-loc(Blazor WebAssembly):::-Runtime über die JavaScript-Interop verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="520c4-160">The :::no-loc(Blazor WebAssembly)::: runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="520c4-161">Die Größe der veröffentlichten App, ihre *Nutzlast* , ist ein wichtiger Leistungsfaktor für die Nutzbarkeit einer App.</span><span class="sxs-lookup"><span data-stu-id="520c4-161">The size of the published app, its *payload size* , is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="520c4-162">Das Herunterladen einer großen App in einen Browser dauert relativ lange, was die Benutzerfreundlichkeit beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="520c4-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="520c4-163">:::no-loc(Blazor WebAssembly)::: optimiert die Nutzlastgröße, um die Downloadzeiten zu verringern:</span><span class="sxs-lookup"><span data-stu-id="520c4-163">:::no-loc(Blazor WebAssembly)::: optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="520c4-164">Nicht verwendeter Code wird aus der App entfernt, wenn sie vom [IL-Trimmer (Intermediate Language)](xref:blazor/host-and-deploy/configure-trimmer) veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="520c4-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="520c4-165">HTTP-Antworten werden komprimiert.</span><span class="sxs-lookup"><span data-stu-id="520c4-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="520c4-166">Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="520c4-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="520c4-167">Nicht verwendeter Code wird aus der App entfernt, wenn sie vom [IL-Linker (Intermediate Language)](xref:blazor/host-and-deploy/configure-linker) veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="520c4-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="520c4-168">HTTP-Antworten werden komprimiert.</span><span class="sxs-lookup"><span data-stu-id="520c4-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="520c4-169">Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="520c4-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## :::no-loc(Blazor Server):::

<span data-ttu-id="520c4-170">:::no-loc(Blazor)::: entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="520c4-170">:::no-loc(Blazor)::: decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="520c4-171">*:::no-loc(Blazor Server):::* bietet Unterstützung für das Hosten von :::no-loc(Razor):::-Komponenten in einer ASP.NET Core-App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="520c4-171">*:::no-loc(Blazor Server):::* provides support for hosting :::no-loc(Razor)::: components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="520c4-172">Aktualisierungen der Benutzeroberfläche werden über eine [:::no-loc(SignalR):::](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="520c4-172">UI updates are handled over a [:::no-loc(SignalR):::](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="520c4-173">Die Runtime verarbeitet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="520c4-173">The runtime handles:</span></span>

* <span data-ttu-id="520c4-174">Senden von Benutzeroberflächenereignissen vom Browser an den Server.</span><span class="sxs-lookup"><span data-stu-id="520c4-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="520c4-175">Anwenden von Benutzeroberflächenupdates, die vom Server zurückgesendet werden, auf die gerenderte Komponente.</span><span class="sxs-lookup"><span data-stu-id="520c4-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="520c4-176">Die Verbindung, die von :::no-loc(Blazor Server)::: für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.</span><span class="sxs-lookup"><span data-stu-id="520c4-176">The connection used by :::no-loc(Blazor Server)::: to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![:::no-loc(Blazor Server)::: führt .NET-Code auf dem Server aus und interagiert über eine :::no-loc(SignalR):::-Verbindung mit dem Dokumentobjektmodell.](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="520c4-178">JavaScript-Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="520c4-178">JavaScript interop</span></span>

<span data-ttu-id="520c4-179">Für Apps, die JavaScript-Bibliotheken und Zugriff auf Browser-APIs von Drittanbietern benötigen, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert.</span><span class="sxs-lookup"><span data-stu-id="520c4-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="520c4-180">Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="520c4-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="520c4-181">C#-Code kann [JavaScript-Code abfragen](xref:blazor/call-javascript-from-dotnet), und JavaScript-Code kann [C#-Code abfragen](xref:blazor/call-dotnet-from-javascript).</span><span class="sxs-lookup"><span data-stu-id="520c4-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="520c4-182">Codefreigabe und .NET Standard</span><span class="sxs-lookup"><span data-stu-id="520c4-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="520c4-183">:::no-loc(Blazor)::: implementiert den [.NET Standard](/dotnet/standard/net-standard), über den :::no-loc(Blazor):::-Projekte auf Bibliotheken verweisen können, die den .NET Standard-Spezifikationen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="520c4-183">:::no-loc(Blazor)::: implements the [.NET Standard](/dotnet/standard/net-standard), which enables :::no-loc(Blazor)::: projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="520c4-184">.NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="520c4-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="520c4-185">.NET-Standardklassenbibliotheken können auf verschiedenen .NET-Plattformen wie :::no-loc(Blazor):::, .NET Framework, .NET Core, Xamarin, Mono und Unity freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="520c4-185">.NET Standard class libraries can be shared across different .NET platforms, such as :::no-loc(Blazor):::, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="520c4-186">APIs, die nicht in einem Webbrowser angewendet werden können (z.B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets und für das Threading), lösen die Ausnahme <xref:System.PlatformNotSupportedException> aus.</span><span class="sxs-lookup"><span data-stu-id="520c4-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="520c4-187">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="520c4-187">Additional resources</span></span>

* [<span data-ttu-id="520c4-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="520c4-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="520c4-189">Leitfaden für C#</span><span class="sxs-lookup"><span data-stu-id="520c4-189">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="520c4-190">HTML</span><span class="sxs-lookup"><span data-stu-id="520c4-190">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="520c4-191">[Awesome :::no-loc(Blazor):::](https://github.com/AdrienTorris/awesome-blazor) Community-Links</span><span class="sxs-lookup"><span data-stu-id="520c4-191">[Awesome :::no-loc(Blazor):::](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
