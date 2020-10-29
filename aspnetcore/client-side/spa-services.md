---
title: Verwenden von JavaScript Services zum Erstellen von Single-Page-Anwendungen in ASP.NET Core
author: scottaddie
description: Erfahren Sie mehr über die Vorteile von JavaScript Services zum Erstellen einer von ASP.NET Core gestützten Single-Page-Anwendung (SPA).
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
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
uid: client-side/spa-services
ms.openlocfilehash: 33ae16c033142aa1c0f4cea53ca746e8a6aaf052
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690658"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a>Verwenden von JavaScript Services zum Erstellen von Single-Page-Anwendungen in ASP.NET Core

Von [Scott Addie](https://github.com/scottaddie) und [Fiyaz Hasan](https://fiyazhasan.me/)

Eine Single-Page-Anwendung (SPA) ist aufgrund ihrer inhärenten umfassenden Benutzerumgebung ein gängiger Webanwendungstyp. Die Integration von clientseitigen SPA-Frameworks oder -Bibliotheken wie [Angular](https://angular.io/) oder [React](https://facebook.github.io/react/) mit serverseitigen Frameworks wie ASP.NET Core kann schwierig sein. JavaScript Services wurde entwickelt, um eine reibungslose Integration zu ermöglichen. Die Lösung ermöglicht einen nahtlosen Betrieb zwischen den verschiedenen Client- und Servertechnologiestacks.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> Die in diesem Artikel beschriebenen Funktionen sind ab ASP.NET Core 3.0 veraltet. Im NuGet-Paket [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) steht ein einfacherer Integrationsmechanismus für SPA-Frameworks zur Verfügung. Weitere Informationen finden Sie unter [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890) ([Ankündigung] Ablösung von Microsoft.AspNetCore.SpaServices und Microsoft.AspNetCore.NodeServices).

::: moniker-end

## <a name="what-is-javascript-services"></a>Was ist JavaScript Services?

JavaScript Services sind eine Sammlung von clientseitigen Technologien für ASP.NET Core. Ziel ist es, ASP.NET Core als bevorzugte serverseitige Plattform für den Aufbau von SPAs zu positionieren.

JavaScript Services besteht aus zwei unterschiedlichen NuGet-Paketen:

* [Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)
* [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)

Diese Pakete sind in folgenden Szenarios nützlich:

* Ausführen von JavaScript auf dem Server
* Verwenden eines SPA-Frameworks bzw. einer SPA-Bibliothek
* Erstellen von clientseitigen Ressourcen mit Webpack

Der Schwerpunkt dieses Artikels liegt auf der Verwendung des SpaServices-Pakets.

## <a name="what-is-spaservices"></a>Was ist SpaServices?

SpaServices wurde konzipiert, um ASP.NET Core als bevorzugte serverseitige Plattform für den Aufbau von SPAs zu positionieren. SpaServices ist für die Entwicklung von SPAs mit ASP.NET Core nicht erforderlich und bindet Entwickler nicht an ein bestimmtes Clientframework.

SpaServices bietet nützliche Infrastrukturen wie:

* [Serverseitiges Prerendering](#server-side-prerendering)
* [Webpack Dev Middleware](#webpack-dev-middleware)
* [Hot Module Replacement](#hot-module-replacement)
* [Hilfsprogramme für das Routing](#routing-helpers)

Zusammengenommen verbessern diese Infrastrukturkomponenten sowohl den Entwicklungsvorgang als auch die Laufzeitumgebung. Die Komponenten können individuell angepasst werden.

## <a name="prerequisites-for-using-spaservices"></a>Voraussetzungen für das Verwenden von SpaServices

Für die Arbeit mit SpaServices muss Folgendes installiert sein:

* [Node.js](https://nodejs.org/) (Version 6 oder höher) mit npm

  * Um sicherzustellen, dass diese Komponenten installiert sind und gefunden werden können, führen Sie den folgenden Befehl über die Befehlszeile aus:

    ```console
    node -v && npm -v
    ```

  * Wenn die Bereitstellung auf einer Azure-Website erfolgt, ist keine Aktion erforderlich &mdash; Node.js ist installiert und in den Serverumgebungen verfügbar.

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * Unter Windows mit Visual Studio 2017 wird das SDK installiert, indem Sie die Workload **Plattformübergreifende .NET Core-Entwicklung** auswählen.

* NuGet-Paket [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)

## <a name="server-side-prerendering"></a>Serverseitiges Prerendering

Eine universelle (oder auch isomorphe) Anwendung ist eine JavaScript-Anwendung, die sowohl auf dem Server als auch auf dem Client ausgeführt werden kann. Angular, React und andere beliebte Frameworks bieten eine universelle Plattform für diese Art der Anwendungsentwicklung. Das Konzept besteht darin, zunächst die Frameworkkomponenten auf dem Server über Node.js zu rendern und dann die weitere Ausführung an den Client zu delegieren.

[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) für ASP.NET Core von SpaServices vereinfachen die Implementierung von serverseitigem Prerendering, indem die JavaScript-Funktionen auf dem Server aufgerufen werden.

### <a name="server-side-prerendering-prerequisites"></a>Voraussetzungen für serverseitiges Prerendering

Installieren Sie das npm-Paket [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering):

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a>Konfiguration des serverseitigen Prerenderings

Die Taghilfsprogramme werden durch die Registrierung des Namespaces in der Datei *_ViewImports.cshtml* des Projekts zugänglich gemacht:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

Diese Taghilfsprogramme beseitigen die Komplexität der direkten Kommunikation mit Low-Level-APIs, indem sie eine HTML-ähnliche Syntax innerhalb der Razor-Ansicht verwenden:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a>Taghilfsprogramm „asp-prerender-module“

Das Taghilfsprogramm `asp-prerender-module`, das im vorherigen Codebeispiel verwendet wird, führt *ClientApp/dist/main-server. js* auf dem Server über Node.js aus. Der Übersichtlichkeit halber ist die Datei *main-server.js* ein Artefakt der TypeScript-zu-JavaScript-Transpilationstask im [Webpack](https://webpack.github.io/)-Erstellungsprozess. Webpack definiert einen Einstiegspunktalias von `main-server`; und die Durchquerung des Abhängigkeitsdiagramms für diesen Alias beginnt bei der Datei *ClientApp/boot-server.ts* :

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

Im folgenden Angular-Beispiel verwendet die Datei *ClientApp/boot-server.ts* die Funktion `createServerRenderer` und den Typ `RenderResult` des npm-Pakets `aspnet-prerendering`, um das Serverrendering über Node.js zu konfigurieren. Das HTML-Markup, das für das serverseitige Rendering bestimmt ist, wird an einen Auflösungsfunktionsaufruf übergeben, der ein stark typisiertes JavaScript-`Promise`-Objekt umschließt. Wichtig ist, dass das `Promise`-Objekt das HTML-Markup asynchron an die Seite übermittelt, damit es in das DOM-Platzhalterelement eingefügt werden kann.

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a>Taghilfsprogramm „asp-prerender-data“

In Verbindung mit dem Taghilfsprogramm `asp-prerender-module` kann das Taghilfsprogramm `asp-prerender-data` verwendet werden, um kontextbezogene Informationen aus der Razor-Ansicht an den serverseitigen JavaScript-Code zu übergeben. Das folgende Markup übergibt z. B. Benutzerdaten an das `main-server`-Modul:

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

Das empfangene `UserName`-Argument wird mit dem integrierten JSON-Serialisierungsmodul serialisiert und im `params.data`-Objekt gespeichert. Im folgenden Angular-Beispiel werden die Daten verwendet, um einen personalisierten Gruß innerhalb eines `h1`-Elements zu erstellen:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

Eigenschaftsnamen, die in Taghilfsprogramme übergeben werden, werden mit **PascalCase** -Notation dargestellt. Dies steht im Gegensatz zu JavaScript, wo die gleichen Eigenschaftsnamen mit **camelCase** dargestellt werden. Dieser Unterschied ist auf die Standardkonfiguration der JSON-Serialisierung zurückzuführen.

Um das vorherige Codebeispiel zu erweitern, können Daten vom Server an die Ansicht durch Umwandlung der `globals`-Eigenschaft, die der `resolve`-Funktion zur Verfügung gestellt wird, übergeben werden:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

Das innerhalb des `globals`-Objekts definierte `postList`-Array wird an das globale `window`-Objekt des Browsers angefügt. Durch dieses Verschieben der Variablen in den globalen Bereich wird doppelter Arbeitsaufwand vermieden, insbesondere wenn dieselben Daten einmal auf den Server und ein zweites Mal auf den Client geladen werden.

![Globale postList-Variable, angefügt an window-Objekt](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a>Webpack Dev Middleware

[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) führt einen optimierten Entwicklungsworkflow ein, wobei Webpack Ressourcen nach Bedarf erstellt. Die Middleware kompiliert und bedient automatisch die clientseitigen Ressourcen, wenn eine Seite im Browser neu geladen wird. Der alternative Ansatz besteht darin, Webpack manuell über das npm-Buildskript des Projekts aufzurufen, wenn sich eine Abhängigkeit von einem Drittanbieter oder der benutzerdefinierte Code ändert. Ein npm-Buildskript in der Datei *package.json* wird im folgenden Beispiel gezeigt:

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a>Voraussetzungen für Webpack Dev Middleware

Installieren Sie das npm-Paket [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack):

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a>Konfiguration von Webpack Dev Middleware

Webpack Dev Middleware wird in der HTTP-Anforderungspipeline über den folgenden Code in der Datei *Startup.cs* mit der `Configure`-Methode registriert:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

Die `UseWebpackDevMiddleware`-Erweiterungsmethode muss aufgerufen werden, bevor das [Registrieren von statischem Dateihosting](xref:fundamentals/static-files) über die `UseStaticFiles`-Erweiterungsmethode erfolgt. Aus Sicherheitsgründen sollten Sie die Middleware nur registrieren, wenn die App im Entwicklungsmodus ausgeführt wird.

Die Eigenschaft `output.publicPath` der Datei *webpack.config.js* weist die Middleware an, den Ordner `dist` auf Änderungen zu überwachen:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a>Hot Module Replacement

Stellen Sie sich die Funktion [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) von Webpack als eine Weiterentwicklung von [Webpack Dev Middleware](#webpack-dev-middleware) vor. HMR bietet dieselben Vorteile, optimiert aber den Entwicklungsworkflow zusätzlich, indem der Seiteninhalt nach der Zusammenstellung der Änderungen automatisch aktualisiert wird. Verwechseln Sie dies nicht mit einer Aktualisierung des Browsers, die den aktuellen In-Memory-Status und die Debugsitzung der SPA beeinträchtigen würde. Es gibt einen Livelink zwischen dem Webpack Dev Middleware-Dienst und dem Browser, was bedeutet, dass Änderungen in den Browser per Push übertragen werden.

### <a name="hot-module-replacement-prerequisites"></a>Voraussetzungen für Hot Module Replacement

Installieren Sie das npm-Paket [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware):

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a>Konfiguration von Hot Module Replacement

Die HMR-Komponente muss in der HTTP-Anforderungspipeline von MVC in der `Configure`-Methode registriert sein:

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

Wie bei der [Webpack Dev Middleware](#webpack-dev-middleware) muss die `UseWebpackDevMiddleware`-Erweiterungsmethode vor der `UseStaticFiles`-Erweiterungsmethode aufgerufen werden. Aus Sicherheitsgründen sollten Sie die Middleware nur registrieren, wenn die App im Entwicklungsmodus ausgeführt wird.

Die Datei *webpack.config.js* muss ein `plugins`-Array definieren, auch wenn es leer gelassen wurde:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

Nach dem Laden der Anwendung im Browser sehen Sie auf der Registerkarte „Konsole“ der Entwicklertools eine Bestätigung der HMR-Aktivierung:

![Verbindungsmeldung von Hot Module Replacement](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a>Hilfsprogramme für das Routing

In den meisten ASP.NET Core-basierten SPAs wird neben dem serverseitigen Routing oft auch ein clientseitiges Routing gewünscht. Die SPA- und MVC-Routingsysteme können unabhängig und ohne Beeinträchtigung betrieben werden. Es gibt jedoch einen Grenzfall, der eine Herausforderung darstellt: die Identifizierung von 404-HTTP-Antworten.

Denken Sie an das Szenario, in dem eine Route ohne Erweiterung von `/some/page` verwendet wird. Angenommen, die Anforderung stimmt nicht mit dem Muster einer serverseitigen Route überein, aber deren Muster stimmt mit einer clientseitigen Route überein. Stellen Sie sich nun eine eingehende Anforderung für `/images/user-512.png` vor, bei der in der Regel davon ausgegangen wird, dass sie eine Bilddatei auf dem Server findet. Wenn dieser angeforderte Ressourcenpfad mit keiner serverseitigen Route oder statischen Datei übereinstimmt, ist es unwahrscheinlich, dass die clientseitige Anwendung ihn verarbeiten würde, da normalerweise ein 404-HTTP-Statuscode zurückgegeben werden soll.

### <a name="routing-helpers-prerequisites"></a>Voraussetzungen für Hilfsprogramme für das Routing

Installieren Sie das clientseitige npm-Paket für das Routing. Verwenden von Angular als Beispiel:

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a>Konfiguration der Hilfsprogramme für das Routing

Eine Erweiterungsmethode mit dem Namen `MapSpaFallbackRoute` wird in der `Configure`-Methode verwendet:

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

Routen werden in der Reihenfolge ausgewertet, in der sie konfiguriert sind. Folglich wird die `default`-Route im vorangehenden Codebeispiel zuerst für den Musterabgleich verwendet.

## <a name="create-a-new-project"></a>Erstellt ein neues Projekt

JavaScript Services stellt vorkonfigurierte Anwendungsvorlagen bereit. SpaServices wird in diesen Vorlagen zusammen mit verschiedenen Frameworks und Bibliotheken wie Angular, React und Redux verwendet.

Diese Vorlagen können mit dem folgenden Befehl über die .NET Core-CLI installiert werden:

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

Es wird eine Liste der verfügbaren SPA-Vorlagen angezeigt:

| Vorlagen                                 | Kurzname | Sprache | Tags        |
| ------------------------------------------| :--------: | :------: | :---------: |
| MVC ASP.NET Core mit Angular             | angular    | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core mit React.js            | react      | [C#]     | Web/MVC/SPA |
| MVC ASP.NET Core mit React.js und Redux  | reactredux | [C#]     | Web/MVC/SPA |

Wenn Sie ein neues Projekt mit einer der SPA-Vorlagen erstellen möchten, schließen Sie den **Kurznamen** der Vorlage in den [dotnet new](/dotnet/core/tools/dotnet-new)-Befehl ein. Der folgende Befehl erstellt eine Angular-Anwendung mit ASP.NET Core MVC, die für die Serverseite konfiguriert ist:

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a>Festlegen des Laufzeitkonfigurationsmodus

Es gibt zwei primäre Laufzeitkonfigurationsmodi:

* **Entwicklung:**
  * Enthält Quellzuordnungsdateien zum Vereinfachen des Debuggens.
  * Der clientseitige Code wird nicht optimiert, um die Leistung zu verbessern.
* **Produktion:**
  * Schließt Quellzuordnungsdateien aus.
  * Optimiert den clientseitigen Code über Bündelung und Minimierung.

ASP.NET Core verwendet eine Umgebungsvariable namens `ASPNETCORE_ENVIRONMENT`, um den Konfigurationsmodus zu speichern. Weitere Informationen finden Sie unter [Festlegen der Umgebung](xref:fundamentals/environments#set-the-environment).

### <a name="run-with-net-core-cli"></a>Ausführen mit der .NET Core-CLI

Stellen Sie die erforderlichen NuGet- und npm-Pakete wieder her, indem Sie den folgenden Befehl im Stammverzeichnis des Projekts ausführen:

```dotnetcli
dotnet restore && npm i
```

Erstellen Sie die Anwendung, und führen Sie sie aus:

```dotnetcli
dotnet run
```

Die Anwendung startet auf Localhost entsprechend dem [Laufzeitkonfigurationsmodus](#set-the-runtime-configuration-mode). Wenn Sie im Browser zu `http://localhost:5000` navigieren, wird die Landing Page angezeigt.

### <a name="run-with-visual-studio-2017"></a>Ausführen mit Visual Studio 2017

Öffnen Sie die Datei *.csproj* , die mit dem [dotnet new](/dotnet/core/tools/dotnet-new)-Befehl generiert wurde. Die erforderlichen NuGet- und npm-Pakete werden beim Öffnen des Projekts automatisch wiederhergestellt. Dieser Wiederherstellungsprozess kann einige Minuten in Anspruch nehmen, und die Anwendung ist nach Abschluss des Vorgangs einsatzbereit. Klicken Sie auf die grüne Schaltfläche zum Ausführen, oder drücken Sie `Ctrl + F5`, und der Browser öffnet die Landing Page der Anwendung. Die Anwendung wird auf Localhost entsprechend dem [Laufzeitkonfigurationsmodus](#set-the-runtime-configuration-mode) ausgeführt.

## <a name="test-the-app"></a>Testen der App

SpaServices-Vorlagen sind vorkonfiguriert, um clientseitige Tests mit [Karma](https://karma-runner.github.io/1.0/index.html) und [Jasmine](https://jasmine.github.io/) auszuführen. Jasmine ist ein beliebtes Komponententestframework für JavaScript, wohingegen Karma ein Test Runner für diese Tests ist. Karma ist für die Verwendung mit [Webpack Dev Middleware](#webpack-dev-middleware) konfiguriert, sodass Entwickler nicht bei jeder Änderung den Test anhalten und ausführen müssen. Der Test wird automatisch ausgeführt, unabhängig davon, ob es sich um den Code mit dem Testfall oder den Testfall selbst handelt.

Wenn Sie beispielsweise die Angular-Anwendung verwenden, werden damit zwei Jasmine-Testfälle für `CounterComponent` in der Datei *counter.component.spec.ts* bereitgestellt:

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

Öffnen Sie die Eingabeaufforderung im Verzeichnis *ClientApp* . Führen Sie den folgenden Befehl aus:

```console
npm test
```

Das Skript startet den Karma-Test Runner, der die Einstellungen liest, die in der Datei *karma.conf.js* definiert sind. Neben anderen Einstellungen identifiziert die Datei *karma.conf.js* über das `files`-Array die auszuführenden Testdateien:

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a>Veröffentlichen der App

Weitere Informationen zur Veröffentlichung in Azure finden Sie in [diesem GitHub-Thema](https://github.com/dotnet/AspNetCore.Docs/issues/12474).

Die Zusammenstellung der generierten clientseitigen Ressourcen und der veröffentlichten ASP.NET Core-Artefakte zu einem einsatzbereiten Paket kann umständlich sein. Daher ist es gut, dass SpaServices diesen gesamten Veröffentlichungsprozess mit einem benutzerdefinierten MSBuild-Target namens `RunWebpack` orchestriert:

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

Das MSBuild-Ziel hat die folgenden Zuständigkeiten:

1. Wiederherstellen der npm-Pakete
1. Erstellen eines produktionsfähigen Builds der clientseitigen Drittanbieterressourcen
1. Erstellen eines produktionsfähigen Builds der benutzerdefinierten, clientseitigen Ressourcen
1. Kopieren der von Webpack generierten Ressourcen in den Ordner für die Veröffentlichung

Das MSBuild-Ziel wird aufgerufen, wenn Folgendes ausgeführt wird:

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Angular-Dokumentation](https://angular.io/docs)
