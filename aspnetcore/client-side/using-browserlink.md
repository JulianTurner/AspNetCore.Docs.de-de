---
title: Browserverknüpfung in ASP.NET Core
author: ncarandini
description: Erläutert das Visual Studio-Feature „Browserverknüpfung“, über das sich die Entwicklungsumgebung mit einem oder mehreren Webbrowsern verknüpfen lässt
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054488"
---
# <a name="browser-link-in-aspnet-core"></a>Browserverknüpfung in ASP.NET Core

Von [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson) und [Tom Dykstra](https://github.com/tdykstra)

Browserverknüpfungen sind ein Visual Studio-Feature. Sie stellen einen Kommunikationskanal zwischen der Entwicklungsumgebung und einem oder mehreren Webbrowsern dar. Mit einer Browserverknüpfung können Sie Ihre Web-App in mehreren Browsern gleichzeitig aktualisieren, was für browserübergreifende Tests nützlich ist.

## <a name="browser-link-setup"></a>Einrichtung einer Browserverknüpfung

::: moniker range=">= aspnetcore-3.0"

Fügen Sie Ihrem Projekt das Paket [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) hinzu. Aktivieren Sie für ASP.NET Core-Projekte mit Razor Pages oder MVC außerdem die Laufzeitkompilierung von Razor-Dateien ( *.cshtml*). Dieser Vorgang wird unter <xref:mvc/views/view-compilation> beschrieben. Razor-Syntaxänderungen werden nur bei aktivierter Laufzeitkompilierung angewendet.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

Wenn Sie ein ASP.NET Core 2.0-Projekt in ein ASP.NET Core 2.1-Projekt konvertieren und auf das [Metapaket „Microsoft.AspNetCore.App“](xref:fundamentals/metapackage-app) umstellen, installieren Sie das Paket [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) für das Feature „Browserverknüpfung“. In ASP.NET Core 2.1-Projektvorlagen wird standardmäßig das Metapaket `Microsoft.AspNetCore.App` verwendet.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

In den ASP.NET Core 2.0-Projektvorlagen **Webanwendung**, **Leer** und **Web-API** wird das [Metapaket „Microsoft.AspNetCore.All“](xref:fundamentals/metapackage) verwendet, das einen Paketverweis auf [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) enthält. Daher ist für das Metapaket `Microsoft.AspNetCore.All` keine weitere Aktion erforderlich, um Browserverknüpfungen verwendbar zu machen.

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

Die ASP.NET Core 1.x-Projektvorlage **Webanwendung** verfügt über einen Paketverweis auf das Paket [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/). Bei anderen Projekttypen müssen Sie `Microsoft.VisualStudio.Web.BrowserLink` den Paketverweis manuell hinzufügen.

::: moniker-end

### <a name="configuration"></a>Konfiguration

Rufen Sie `UseBrowserLink` in der `Startup.Configure`-Methode auf:

```csharp
app.UseBrowserLink();
```

Der Befehl `UseBrowserLink` wird in der Regel in einem `if`-Block platziert, mit dem Browserverknüpfungen nur in der Entwicklungsumgebung aktiviert werden. Zum Beispiel:

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

Weitere Informationen finden Sie unter <xref:fundamentals/environments>.

## <a name="how-to-use-browser-link"></a>Verwenden von Browserverknüpfungen

Wenn Sie ein ASP.NET Core-Projekt geöffnet haben, zeigt Visual Studio das Symbolleisten-Steuerelement für Browserverknüpfungen neben dem Symbolleisten-Steuerelement **Debugziel** an:

![Dropdownmenü für Browserverknüpfungen](using-browserlink/_static/browserLink-dropdown-menu.png)

Über das Symbolleisten-Steuerelement für Browserverknüpfungen können Sie folgende Aktionen ausführen:

* Aktualisieren einer Web-App in mehreren Browsern gleichzeitig
* Öffnen des **Dashboards für Browserverknüpfungen**
* Aktivieren/Deaktivieren von **Browserverknüpfungen**. Hinweis: Browserverknüpfungen sind in Visual Studio standardmäßig deaktiviert.
* Aktivieren oder Deaktivieren der [automatischen CSS-Synchronisierung](#enable-or-disable-css-auto-sync)

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a>Aktualisieren einer Web-App in mehreren Browsern gleichzeitig

Wenn Sie einen einzelnen Webbrowser auswählen möchten, der beim Starten des Projekts gestartet werden soll, verwenden Sie das Dropdownmenü im Symbolleisten-Steuerelement **Debugziel**:

![F5-Dropdownmenü](using-browserlink/_static/debug-target-dropdown-menu.png)

Zum gleichzeitigen Öffnen mehrerer Browser klicken Sie in der Dropdownliste auf **Browserauswahl...** . Halten Sie die Taste <kbd>STRG</kbd> gedrückt, um die gewünschten Browser auszuwählen, und klicken Sie dann auf **Durchsuchen**:

![Gleichzeitiges Öffnen mehrerer Browser](using-browserlink/_static/open-many-browsers-at-once.png)

Der folgende Screenshot zeigt Visual Studio mit geöffneter Indexansicht und zwei geöffneten Browsern:

![Beispiel für die Synchronisierung mit zwei Browsern](using-browserlink/_static/sync-with-two-browsers-example.png)

Zeigen Sie auf das Symbolleisten-Steuerelement für Browserverknüpfungen, um sich die Browser anzeigen zu lassen, die mit dem Projekt verbunden sind:

![Hovertipp](using-browserlink/_static/hoover-tip.png)

Wenn Sie etwas in der Indexansicht ändern und anschließend im Symbolleisten-Steuerelement für Browserverknüpfungen auf die Schaltfläche zum Aktualisieren klicken, werden alle verbundenen Browser aktualisiert:

![Browser werden mit Änderungen synchronisiert](using-browserlink/_static/browsers-sync-to-changes.png)

Browserverknüpfungen funktionieren auch, wenn Sie Browser außerhalb von Visual Studio starten und zur App-URL navigieren.

### <a name="the-browser-link-dashboard"></a>Das Dashboard für Browserverknüpfungen

Öffnen Sie im Featuredropdownmenü das Fenster **Browserlink-Dashboard**, um die Verbindung mit geöffneten Browsern zu verwalten:

![Dashboard für Browserverknüpfungen öffnen](using-browserlink/_static/open-browserlink-dashboard.png)

Wenn kein Browser verbunden ist, können Sie eine Nichtdebuggingsitzung starten, indem Sie auf den Link **In Browser anzeigen** klicken:

![Dashboard für Browserverknüpfungen: keine Verbindungen](using-browserlink/_static/browserlink-dashboard-no-connections.png)

Andernfalls werden die verbundenen Browser mit dem Pfad zu der Seite angezeigt, die von den einzelnen Browsern angezeigt wird:

![Dashboard für Browserverknüpfungen: zwei Verbindungen](using-browserlink/_static/browserlink-dashboard-two-connections.png)

Sie können auch auf einen einzelnen Browsernamen klicken, um nur diesen einen Browser zu aktualisieren.

### <a name="enable-or-disable-browser-link"></a>Aktivieren oder Deaktivieren von Browserverknüpfungen

Wenn Sie die Browserverknüpfungen nach einer Deaktivierung wieder aktivieren, müssen Sie die Browser aktualisieren, um sie neu zu verbinden.

### <a name="enable-or-disable-css-auto-sync"></a>Aktivieren oder Deaktivieren der automatischen CSS-Synchronisierung

Wenn die automatische CSS-Synchronisierung aktiviert ist, werden verbundene Browser automatisch aktualisiert, wenn Sie Änderungen an CSS-Dateien vornehmen.

## <a name="how-it-works"></a>Funktionsweise

Die Browserverknüpfung erstellt mit [SignalR](xref:signalr/introduction) einen Kommunikationskanal zwischen Visual Studio und dem Browser. Wenn die Browserverknüpfung aktiviert ist, fungiert Visual Studio als SignalR-Server, mit dem mehrere Clients (Browser) eine Verbindung herstellen können. Die Browserverknüpfung registriert auch eine Middlewarekomponente in der Anforderungspipeline von ASP.NET Core. Diese Komponente fügt jeder Seitenanforderung des Servers besondere `<script>`-Verweise hinzu. Sie können sich die Skriptverweise anzeigen lassen, indem Sie im Browser auf **Quelltext anzeigen** klicken und zum Ende des `<body>`-Taginhalts scrollen:

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

Ihre Quelldateien werden nicht geändert. Die Middlewarekomponente fügt die Skriptverweise dynamisch ein.

Da der browserseitige Code komplett in JavaScript geschrieben ist, funktioniert er in allen Browsern, die SignalR ohne ein zusätzliches Browser-Plug-In unterstützt.
