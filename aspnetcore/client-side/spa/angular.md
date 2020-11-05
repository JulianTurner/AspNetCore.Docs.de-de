---
title: Verwenden der Angular-Projektvorlage mit ASP.NET Core
author: SteveSandersonMS
description: Erfahren Sie, wie Sie sich mit der Projektvorlage für die Einzelseitenanwendung (Single-Page Application, SPA) von ASP.NET Core für Angular und die Angular-CLI vertraut machen.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
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
uid: spa/angular
ms.openlocfilehash: 2fff0d60b71bbbab9347dbe74cad023264247388
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054566"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a>Verwenden der Angular-Projektvorlage mit ASP.NET Core

Die aktualisierte Angular-Projektvorlage stellt einen geeigneten Anfangspunkt für ASP.NET Core-Apps dar, die Angular und die Angular-CLI für die Implementierung einer umfangreichen, clientseitigen Benutzerschnittstelle (User Interface, UI) verwenden.

Mit der Vorlage können Sie ein ASP.NET Core-Projekt, das als API-Back-End fungieren soll, sowie ein Angular-CLI-Projekt erstellen, das als Benutzerschnittstelle fungieren soll. Die Vorlage bietet den Vorteil, dass beide Projekte in einem App-Projekt gehostet werden können. Folglich kann das App-Projekt als eine einzelne Einheit erstellt und veröffentlicht werden.

## <a name="create-a-new-app"></a>Erstellen einer neuen App

Wenn ASP.NET Core 2.1 auf Ihrem Computer installiert ist, müssen Sie die Vorlage für Angular-Projekte nicht installieren.

Erstellen Sie über eine Eingabeaufforderung mit dem Befehl `dotnet new angular` in einem leeren Verzeichnis ein neues Projekt. Mit den folgenden Befehlen wird die App beispielsweise im Verzeichnis *my-new-app* erstellt und zu diesem Verzeichnis gewechselt:

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

Führen Sie die App über Visual Studio oder die .NET Core CLI aus:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

Öffnen Sie die generierte *CSPROJ* -Datei, und führen Sie die App von dort wie gewohnt aus.

Während der Erstellung werden bei der ersten Ausführung npm-Abhängigkeiten wiederhergestellt. Dies kann einige Minuten dauern. Nachfolgende Builds sind wesentlich schneller.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Stellen Sie sicher, dass Sie über eine Umgebungsvariable mit dem Namen `ASPNETCORE_Environment` und dem Wert `Development` verfügen. Führen Sie unter Windows (bei Eingabeaufforderungen außerhalb von PowerShell) `SET ASPNETCORE_Environment=Development` aus. Führen Sie unter Linux oder macOS `export ASPNETCORE_Environment=Development` aus.

Führen Sie [dotnet build](/dotnet/core/tools/dotnet-build) aus, um zu überprüfen, ob die App ordnungsgemäß erstellt wurde. Während der Erstellung werden bei der ersten Ausführung npm-Abhängigkeiten wiederhergestellt. Dies kann einige Minuten dauern. Nachfolgende Builds sind wesentlich schneller.

Führen Sie [dotnet run](/dotnet/core/tools/dotnet-run) aus, um die App zu starten. Eine der folgenden ähnelnde Meldung wird angezeigt:

```console
Now listening on: http://localhost:<port>
```

Navigieren Sie in einem Browser zu dieser URL.

> [!WARNING]
> Die App startet im Hintergrund eine Instanz des Angular-CLI-Servers. Eine der folgenden ähnelnde Meldung wird angezeigt: *NG Live Development Server lauscht an localhost:&lt;otherport&gt;, öffnen Sie einen Browser für http://localhost:&lt;otherport&gt;/* . Ignorieren Sie die Meldung&mdash;Es handelt sich **nicht** um die URL für die kombinierte ASP.NET Core und Angular-LI-App.

---

Mit der Projektvorlage werden eine ASP.NET Core-App und eine Angular-App erstellt. Die ASP.NET Core-App ist zur Verwendung für den Datenzugriff, die Autorisierung und weitere serverseitige Belange vorgesehen. Die Angular-App, die sich im Unterverzeichnis *ClientApp* befindet, ist für sämtliche Belange der Benutzerschnittstelle vorgesehen.

## <a name="add-pages-images-styles-modules-etc"></a>Hinzufügen von Seiten, Images, Formatvorlagen, Modulen usw.

Das Verzeichnis *ClientApp* enthält eine standardmäßige Angular-CLI-App. Weitere Informationen finden Sie in der offiziellen [Angular-Dokumentation](https://angular.io).

Die Angular-App, die mit dieser Vorlage erstellt wurde, und die App, die von der Angular-CLI selbst (über `ng new`) erstellt wurde, unterscheiden sich geringfügig. Die Funktionen der App sind jedoch unverändert. Die mit der Vorlage erstellte App enthält ein auf [Bootstrap](https://getbootstrap.com/) basiertes Layout und ein Beispiel für grundlegendes Routing.

## <a name="run-ng-commands"></a>Ausführen von ng-Befehlen

Wechseln Sie über eine Eingabeaufforderung zum Unterverzeichnis *ClientApp* :

```console
cd ClientApp
```

Wenn das `ng`-Tool global installiert ist, können Sie seine Befehle ausführen. Sie können z.B. `ng lint`, `ng test` oder einen beliebigen anderen [Angular-CLI-Befehl](https://angular.io/cli) ausführen. Allerdings muss `ng serve` nicht ausgeführt werden, da Ihre ASP.NET Core-App den serverseitigen und den clientseitigen Teil Ihrer App bedient. Intern verwendet sie `ng serve` bei der Entwicklung.

Wenn Sie das `ng`-Tool nicht installiert haben, führen Sie stattdessen `npm run ng` aus. Sie können beispielsweise `npm run ng lint``npm run ng test` ausführen.

## <a name="install-npm-packages"></a>NPM-Pakete installieren

Verwenden Sie für die Installation von npm-Paketen von Drittanbietern eine Eingabeaufforderung im Unterverzeichnis *ClientApp*. Zum Beispiel:

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a>Veröffentlichen und Bereitstellen

Bei der Entwicklung wird die App in einem für Entwickler optimierten Modus ausgeführt. JavaScript-Pakete enthalten beispielsweise Quellzuordnungen (damit Sie beim Debuggen Ihren ursprünglichen TypeScript-Code anzeigen können). Die App überwacht TypeScript-, HTML- und CSS-Dateiänderungen auf dem Datenträger und führt bei Feststellung dieser Dateiänderungen automatisch eine Neukompilierung und ein erneutes Laden durch.

Stellen Sie bei der Produktion eine für Leistung optimierte Version Ihrer App bereit. Dies erfolgt gemäß der Konfiguration automatisch. Beim Veröffentlichen gibt die Buildkonfiguration einen verkleinerten, Ahead-of-Time-kompilierten Build (AOT-Build) Ihres clientseitigen Codes aus. Im Gegensatz zum Entwicklungsbuild muss Node.js beim Produktionsbuild nicht auf dem Server installiert sein (sofern Sie das serverseitige Rendern nicht aktiviert haben).

Sie können [ASP.NET Core-Standardhosting- und -bereitstellungsmethoden](xref:host-and-deploy/index) verwenden.

## <a name="run-ng-serve-independently"></a>Unabhängiges Ausführen von „ng serve“

Das Projekt ist so konfiguriert, dass die eigene Instanz des Angular-CLI-Servers im Hintergrund gestartet wird, wenn die ASP.NET Core-App im Entwicklungsmodus gestartet wird. Dies ist nützlich, da es bedeutet, dass Sie keinen separaten Server manuell ausführen müssen.

Bei diesem Standardsetup gibt es einen Nachteil. Jedes Mal, wenn Sie Ihren C#-Code ändern und Ihre ASP.NET Core-App neu gestartet werden muss, wird auch der Angular-CLI-Server neu gestartet. Die Sicherung wird nach ca. 10 Sekunden gestartet. Wenn Sie Ihren C#-Code häufig bearbeiten und nicht warten möchten, bis der Angular-CLI-Server neu gestartet wurde, können Sie den Angular-CLI-Server unabhängig vom ASP.NET Core-Prozess extern ausführen. Gehen Sie hierzu wie folgt vor:

1. Wechseln Sie in einer Eingabeaufforderung zu dem Unterverzeichnis *ClientApp* , und starten Sie den Angular-CLI-Entwicklungsserver:

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > Starten Sie den Angular-CLI-Entwicklungsserver mit `npm start` und nicht mit `ng serve`, damit die Konfiguration in *package.json* gewahrt wird. Um zusätzliche Parameter an den Angular-CLI-Server zu übergeben, fügen Sie diese der entsprechenden `scripts`-Zeile in der Datei *package.json* hinzu.

2. Ändern Sie Ihre ASP.NET Core-App so, dass die externe Angular-CLI-Instanz verwendet wird, anstatt eine eigene Instanz zu starten. Ersetzen Sie den `spa.UseAngularCliServer`-Aufruf in Ihrer *Startklasse* durch Folgendes:

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

Wenn Sie Ihre ASP.NET Core-App starten, wird kein Angular-CLI-Server gestartet. Stattdessen wird die Instanz verwendet, die Sie manuell gestartet haben. Dadurch kann sie schneller gestartet und neu gestartet werden. Sie müssen also nicht mehr jedes Mal warten, bis Angular-CLI Ihre Client-App neu erstellt hat.

### <a name="pass-data-from-net-code-into-typescript-code"></a>Übergeben von Daten aus .NET Code in TypeScript-Code

Während des SSR empfiehlt es sich, per Anforderung Daten aus der ASP.NET Core-App an die Angular-App zu übergeben. Beispielsweise könnten Sie cookieinformationen übergeben oder etwas aus einer Datenbank lesen. Bearbeiten Sie hierzu die *Startup* -Klasse. Im Rückruf für `UseSpaPrerendering` legen Sie einen Wert für `options.SupplyData` fest, z.B.:

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

Der `SupplyData`-Rückruf ermöglicht Ihnen, beliebige JSON-serialisierbare Daten per Anforderung zu übergeben (z.B. Zeichenfolgen, boolesche Werte oder Zahlen). Der *main.server.ts* -Code erhält diese als `params.data`. Im vorangehenden Codebeispiel wird z.B. ein boolescher Wert als `params.data.isHttpsRequest` an den `createServerRenderer`-Rückruf übergeben. Sie können dies auf eine beliebige Art und Weise, die von Angular unterstützt wird, an andere Teile Ihrer App übergeben. Sehen Sie beispielsweise, wie *main.server.ts* den `BASE_URL`-Wert an eine beliebige Komponente übergibt, deren Konstruktor für den Empfang deklariert wurde.

### <a name="drawbacks-of-ssr"></a>Nachteile von SSR

Nicht alle Apps profitieren von SSR. Der Hauptvorteil ist die wahrgenommene Leistung. Besucher, die Ihre App über eine langsame Netzwerkverbindung oder auf langsamen mobilen Geräten erreichen, finden die ursprüngliche UI schnell, auch wenn das Abrufen und Analysieren der JavaScript-Pakete einige Zeit dauert. Allerdings werden viele SPAs hauptsächlich über schnelle, interne Unternehmensnetzwerke auf schnellen Computern verwendet, auf denen die App nahezu umgehend angezeigt wird.

Gleichzeitig hat die Aktivierung von SSR zahlreiche Nachteile. Sie erhöht die Komplexität des Entwicklungsprozesses. Der Code muss in zwei verschiedenen Umgebungen ausgeführt werden: clientseitig und serverseitig (über ASP.NET Core in einer Node.js-Umgebung aufgerufen). Nachfolgend sind einige Dinge aufgeführt, die Sie berücksichtigen sollten:

* Das SSR erfordert eine Node.js-Installation auf Produktionsservern. Dies ist für einige Bereitstellungsszenarios, wie z.B. Azure App Services, automatisch der Fall, für andere, wie etwa Azure Service Fabric, dagegen nicht.
* Bei Aktivierung des `BuildServerSideRenderer`-Buildflags veröffentlicht das *node_modules* -Verzeichnis. Dieser Ordner enthält mehr als 20.000 Dateien, was die Bereitstellungszeit erhöht.
* Zum Ausführen von Code in einer Node.js-Umgebung kann nicht davon ausgegangen werden, dass Browser-spezifische JavaScript-APIs, wie z.B. `window` oder `localStorage`, vorhanden sind. Wenn Ihr Code (oder eine Bibliothek eines Drittanbieters, auf die Sie verweisen) versucht, diese APIs zu verwenden, erhalten Sie einen Fehler während des SSR. Verwenden Sie beispielsweise jQuery nicht, da es häufig auf Browser-spezifische APIs verweist. Um Fehler auszuschließen, müssen Sie das SSR vermeiden oder aber Browser-spezifische APIs oder Bibliotheken vermeiden. Sie können alle Aufrufe dieser APIs in Überprüfungen einschließen, um sicherzustellen, dass sie nicht während des SSR aufgerufen werden. Verwenden Sie z.B. eine Überprüfung wie die im folgenden JavaScript- oder TypeScript-Code enthaltene:

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authentication/identity/spa>
