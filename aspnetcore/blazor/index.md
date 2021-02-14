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
ms.openlocfilehash: 560fead9868bab9888c0d6a69cf09f135bbf39cc
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279750"
---
# <a name="introduction-to-aspnet-core-blazor"></a>Einführung in ASP.NET Core Blazor

*Willkommen bei Blazor!*

Blazor ist ein Framework zum Erstellen einer interaktiven clientseitigen Webbenutzeroberfläche mit [.NET](/dotnet/standard/tour):

* Erstellen Sie umfassende interaktive Benutzeroberflächen mit [C#](/dotnet/csharp/) anstatt mit [JavaScript](https://www.javascript.com).
* Gemeinsames Verwenden von server- und clientseitiger App-Logik, die in .NET geschrieben wurde.
* Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).
* Integrieren mit modernen Hostingplattformen, z. B. [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)

Die Verwendung von .NET im für die clientseitige Webentwicklung bietet die folgenden Vorteile:

* Schreiben Sie Code in C# anstatt in JavaScript.
* Nutzen Sie das vorhandene .NET-Ökosystem von [.NET-Bibliotheken](/dotnet/standard/class-libraries).
* Geben Sie die App-Logik server- und clientübergreifend frei.
* Profitieren Sie von Leistung, Zuverlässigkeit und Sicherheit von .NET.
* Bleiben Sie mit [Visual Studio](https://visualstudio.microsoft.com) unter Windows, Linux und macOS produktiv.
* Setzen Sie auf gebräuchliche Sprachen, Frameworks und Tools, die stabil, funktionsreich und einfach zu verwenden sind.

## <a name="components"></a>Komponenten

Blazor-Apps basieren auf *Komponenten*. Eine Komponente in Blazor ist ein Element der Benutzeroberfläche, beispielsweise eine Seite, ein Dialogfeld oder ein Formular für die Dateneingabe.

Komponenten sind in [.NET-Assemblys](/dotnet/standard/assembly/) integrierte .NET-C#-Klassen, auf die Folgendes zutrifft:

* Sie definieren die Logik für ein flexibles Rendering der Benutzeroberfläche.
* Sie behandeln Benutzerereignisse.
* Sie können geschachtelt und wiederverwendet werden.
* Sie können als [Razor-Klassenbibliotheken](xref:razor-pages/ui-class) oder [NuGet-Pakete](/nuget/what-is-nuget) freigegeben und verteilt werden.

Die Komponentenklasse wird in der Regel in Form einer [Razor](xref:mvc/views/razor)-Markupseite mit der Dateierweiterung `.razor` geschrieben. Komponenten in Blazor werden formal als *Razor-Komponenten* bezeichnet. Razor ist eine Syntax, mit der HTML-Markup mit C#-Code kombiniert werden kann, ausgerichtet auf die Produktivität der Entwickler. Razor ermöglicht es Ihnen, mit [IntelliSense](/visualstudio/ide/using-intellisense)-Programmierunterstützung in Visual Studio in ein und derselben Datei zwischen HTML-Markup und C# zu wechseln. Razor Pages und MVC verwenden ebenfalls Razor. Im Gegensatz zu Razor Pages und MVC, die auf einem Anforderung/Antwort-Modell aufbauen, werden Komponenten speziell für die clientseitige Benutzeroberflächenlogik und -gestaltung verwendet.

Blazor verwendet natürliche HTML-Tags für die Benutzeroberflächengestaltung. Das folgende Razor-Markup veranschaulicht eine Komponente (`Dialog.razor`), die ein Dialogfeld anzeigt und ein Ereignis verarbeitet, wenn der Benutzer eine Schaltfläche auswählt:

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

Im obigen Beispiel ist `OnYes` eine C#-Methode, die durch das Schaltflächenereignis `onclick` ausgelöst wird. Der Text (`ChildContent`) und der Titel (`Title`) des Dialogfelds werden von der folgenden Komponente bereitgestellt, die diese Komponente in ihrer Benutzeroberfläche verwendet.

Die Komponente `Dialog` ist mithilfe eines HTML-Tags in einer anderen Komponente geschachtelt. Im folgenden Beispiel verwendet die `Index`-Komponente (`Pages/Index.razor`) die vorherige `Dialog`-Komponente. Das `Title`-Attribut des Tags übergibt einen Wert für den Titel an die `Dialog`-Eigenschaft der `Title`-Komponente.  Der Text der `Dialog`-Komponente (`ChildContent`) wird durch den Inhalt des `<Dialog>`-Elements festgelegt. Wenn die `Dialog`-Komponente der `Index`-Komponente hinzugefügt wird, beschleunigt [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) die Entwicklung mithilfe von Syntax- und Parametervervollständigung.

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

Das Dialogfeld wird gerendert, wenn die `Index`-Komponente in einem Browser aufgerufen wird. Wenn ein Benutzer die Schaltfläche auswählt, zeigt die Browserkonsole mit den Entwicklertools die von der `OnYes`-Methode geschriebene Meldung:

![Dialogfeldkomponente, die im Browser geschachtelt in der Indexkomponente gerendert wird. Die Browserkonsole mit den Entwicklertools zeigt die vom C#-Code geschriebene Meldung an, wenn der Benutzer die Schaltfläche „Ja!“ auf der Benutzeroberfläche auswählt.](index/_static/dialog.png)

Die Komponenten werden in einer In-Memory-Darstellung des [Dokumentobjektmodells](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) (DOM) des Browsers gerendert, die als *Renderstruktur* bezeichnet wird. Mit dieser Struktur lässt sich die Benutzeroberfläche flexibel und effizient aktualisieren.

## Blazor WebAssembly

Blazor WebAssembly ist ein [Single-Page-App-Framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) zum Erstellen von interaktiven clientseitigen Web-Apps mit .NET. Blazor WebAssembly verwendet offene Webstandards ohne Plug-Ins und ohne dass Code in anderen Sprachen neu kompiliert werden muss. Blazor WebAssembly funktioniert in allen modernen Webbrowsern, einschließlich mobiler Browser.

Das Ausführen von .NET-Code in einem Webbrowser wird durch [WebAssembly](https://webassembly.org) (Kurzform: `wasm`) ermöglicht. Es handelt sich um ein Bytecodeformat, das für schnelles Downloaden und die maximale Ausführungsgeschwindigkeit optimiert ist. WebAssembly ist ein offener Webstandard, der in Webbrowsern ohne Plug-Ins unterstützt wird.

WebAssembly-Code kann über JavaScript auf alle Funktionen des Browsers zugreifen. Dies wird als *JavaScript-Interoperabilität* bezeichnet (häufig als *JavaScript Interop* oder *JS Interop* abgekürzt). Über WebAssembly im Browser ausgeführter .NET-Code wird in der JavaScript-Sandbox des Browsers ausgeführt. Hierbei greifen die Schutzmaßnahmen der Sandbox gegen schädliche Aktionen auf dem Clientcomputer.

![Blazor WebAssembly führt .NET-Code unter Verwendung von WebAssembly im Browser aus.](index/_static/blazor-webassembly.png)

Folgendes geschieht, wenn eine Blazor WebAssembly-App in einem Browser erstellt und ausgeführt wird:

* C#-Codedateien und Razor-Dateien werden in .NET-Assemblys kompiliert.
* Die Assemblys und die [.NET-Runtime](/dotnet/framework/get-started/overview) werden in den Browser heruntergeladen.
* Blazor WebAssembly startet die .NET-Runtime und konfiguriert die Runtime zum Laden der Assemblys für die App. DOM-Änderungen und API-Aufrufe im Browser werden von der Blazor WebAssembly-Runtime über die JavaScript-Interop verarbeitet.

Die Größe der veröffentlichten App, ihre *Nutzlastgröße*, ist ein wichtiger Leistungsfaktor für die Verwendbarkeit einer App. Das Herunterladen einer großen App in einen Browser dauert relativ lange, was die Benutzerfreundlichkeit beeinträchtigt. Blazor WebAssembly optimiert die Nutzlastgröße, um die Downloadzeiten zu verringern:

::: moniker range=">= aspnetcore-5.0"

* Nicht verwendeter Code wird aus der App entfernt, wenn sie vom [IL-Trimmer (Intermediate Language)](xref:blazor/host-and-deploy/configure-trimmer) veröffentlicht wird.
* HTTP-Antworten werden komprimiert.
* Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Nicht verwendeter Code wird aus der App entfernt, wenn sie vom [IL-Linker (Intermediate Language)](xref:blazor/host-and-deploy/configure-linker) veröffentlicht wird.
* HTTP-Antworten werden komprimiert.
* Die .NET-Runtime und die Assemblys werden im Browser zwischengespeichert.

::: moniker-end

## Blazor Server

Blazor entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche. *Blazor Server* bietet Unterstützung für das Hosten von Razor-Komponenten in einer ASP.NET Core-App auf dem Server. Aktualisierungen der Benutzeroberfläche werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.

Die Runtime verbleibt auf dem Server und führt folgende Aufgaben aus:

* Ausführen des C#-Codes der App
* Senden von Benutzeroberflächenereignissen vom Browser an den Server.
* Anwenden von Benutzeroberflächenupdates, die vom Server zurückgesendet werden, auf die gerenderte Komponente.

Die Verbindung, die von Blazor Server für die Kommunikation mit dem Browser verwendet wird, wird auch für die Verarbeitung von JavaScript-Interopaufrufen verwendet.

![Blazor Server führt .NET-Code auf dem Server aus und interagiert über eine SignalR-Verbindung mit dem Dokumentobjektmodell.](index/_static/blazor-server.png)

## <a name="javascript-interop"></a>JavaScript-Interoperabilität

Für Apps, die JavaScript-Bibliotheken und Zugriff auf Browser-APIs von Drittanbietern benötigen, sind die Komponenten für die Interoperabilität mit JavaScript konzipiert. Komponenten können jede Bibliothek oder API verwenden, die auch von JavaScript verwendet werden kann. C#-Code kann [JavaScript-Code abfragen](xref:blazor/call-javascript-from-dotnet), und JavaScript-Code kann [C#-Code abfragen](xref:blazor/call-dotnet-from-javascript).

## <a name="code-sharing-and-net-standard"></a>Codefreigabe und .NET Standard

Blazor implementiert den [.NET Standard](/dotnet/standard/net-standard), über den Blazor-Projekte auf Bibliotheken verweisen können, die den .NET Standard-Spezifikationen entsprechen. .NET Standard ist eine formale Spezifikation von .NET-APIs, die häufig für .NET-Implementierungen verwendet werden. .NET-Standardklassenbibliotheken können auf verschiedenen .NET-Plattformen wie Blazor, .NET Framework, .NET Core, Xamarin, Mono und Unity freigegeben werden.

APIs, die nicht in einem Webbrowser angewendet werden können (z.B. zum Zugreifen auf ein Dateisystem, zum Öffnen eines Sockets und für das Threading), lösen die Ausnahme <xref:System.PlatformNotSupportedException> aus.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [WebAssembly](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [Leitfaden für C#](/dotnet/csharp/)
* [Razor-Syntaxreferenz für ASP.NET Core](xref:mvc/views/razor)
* [HTML](https://www.w3.org/html/)
* [Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) Community-Links
