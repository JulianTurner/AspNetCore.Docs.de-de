---
title: Erstellen progressiver Webanwendungen mit Blazor WebAssembly für ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor-basierte progressive Web-App (PWA) erstellen, die moderne Browserfeatures verwendet, um das Verhalten von Desktop-Apps anzunehmen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/11/2020
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
uid: blazor/progressive-web-app
ms.openlocfilehash: 1706d3502dc68f1c25e0c35ba8f5dd44b55ce690
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658650"
---
# <a name="build-progressive-web-applications-with-aspnet-core-blazor-webassembly"></a>Erstellen progressiver Webanwendungen mit Blazor WebAssembly für ASP.NET Core

Von [Steve Sanderson](https://github.com/SteveSandersonMS)

Eine progressive Webanwendung (PWA) ist in der Regel eine Single-Page-Webanwendung (SPA), die mithilfe moderner Browser-APIs und -Funktionen das gleiche Verhalten wie eine Desktop-App aufweist. Blazor WebAssembly ist eine auf Standards basierende clientseitige Web-App-Plattform, die beliebige Browser-APIs einschließlich der PWA-APIs verwenden kann, die für die folgenden Funktionen erforderlich sind:

* Die Fähigkeit, unabhängig von der Netzwerkgeschwindigkeit, mit sofortigem Laden offline zu arbeiten
* Ausführung in einem eigenen App-Fenster, nicht nur in einem Browserfenster
* Starten über das Startmenü, das Dock oder den Startbildschirm des Hostbetriebssystems
* Empfangen von Pushbenachrichtigungen von einem Back-End-Server, auch wenn der Benutzer die App nicht verwendet
* Automatische Updates im Hintergrund

Solche Apps werden aus folgenden Gründen mit dem Wort *progressiv* beschrieben:

* Möglicherweise entdeckt ein Benutzer die App wie jede andere SPA zunächst im Webbrowser.
* Später installiert der Benutzer sie auf seinem Betriebssystem und aktiviert die Pushbenachrichtigungen.

## <a name="create-a-project-from-the-pwa-template"></a>Erstellen eines neuen Projekts aus der PWA-Vorlage

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aktivieren Sie das Kontrollkästchen **Progressive Web Application** (Progressive Web-App) beim Erstellen einer neuen **Blazor WebAssembly-App** im Dialogfeld **Neues Projekt erstellen**:

![Ausgewähltes Kontrollkästchen „Progressive Web Application“ im Dialogfeld „Neues Projekt erstellen“ von Visual Studio](progressive-web-app/_static/image1.png)

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

-->

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code / .NET Core-CLI](#tab/visual-studio-code+netcore-cli)

Verwenden Sie den folgenden Befehl, um ein PWA-Projekt in der Befehlsshell mit der `--pwa`-Option zu erstellen:

```dotnetcli
dotnet new blazorwasm -o MyBlazorPwa --pwa
```

Im vorherigen Befehl erstellt die `-o|--output`-Option einen neuen Ordner für die App namens `MyBlazorPwa`.

---

Die PWA kann optional für eine App konfiguriert werden, die über die gehostete ASP.NET Core-Vorlage erstellt wurde. Das PWA-Szenario ist unabhängig vom Hostmodell.

## <a name="convert-an-existing-blazor-webassembly-app-into-a-pwa"></a>Konvertieren einer vorhandenen Blazor WebAssembly-App in eine PWA

In diesem Abschnitt finden Sie einen Leitfaden zum Konvertieren einer vorhandenen Blazor WebAssembly-App in eine PWA.

In der Projektdatei der App:

* Fügen Sie `PropertyGroup` die folgende `ServiceWorkerAssetsManifest`-Eigenschaft hinzu:

  ```xml
    ...
    <ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
  </PropertyGroup>
   ```

* Fügen Sie `ItemGroup` das folgende `ServiceWorker`-Element hinzu:

  ```xml
  <ItemGroup>
    <ServiceWorker Include="wwwroot\service-worker.js" 
      PublishedContent="wwwroot\service-worker.published.js" />
  </ItemGroup>
  ```

Verwenden Sie **einen** der folgenden Ansätze, um statische Objekte abzurufen:

::: moniker range=">= aspnetcore-5.0"

* Erstellen Sie ein eigenständiges, neues PWA-Projekt. Verwenden Sie dazu den [`dotnet new`](/dotnet/core/tools/dotnet-new)-Befehl in einer Befehlsshell:

  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa
  ```
  
  Im vorherigen Befehl erstellt die `-o|--output`-Option einen neuen Ordner für die App namens `MyBlazorPwa`.
  
  Wenn Sie keine App für das aktuelle Release konvertieren, übergeben Sie die `-f|--framework`-Option. Im folgenden Beispiel wird die App für die .NET Core-Version 3.1 erstellt:
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```

* Navigieren Sie zum GitHub-Repository für ASP.NET Core unter der folgenden URL, die zur Verweisquelle und den Ressourcen für das 5.0-Release führt. Wenn Sie keine App für das 5.0-Release konvertieren, wählen Sie das Release, mit dem Sie arbeiten, aus der Dropdownliste **Switch branches or tags** aus, das für Ihre App gültig ist.

  [dotnet/aspnetcore (Release 5.0): Blazor WebAssembly-Projektvorlagenordner `wwwroot`](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Erstellen Sie ein eigenständiges, neues PWA-Projekt. Verwenden Sie dazu den [`dotnet new`](/dotnet/core/tools/dotnet-new)-Befehl in einer Befehlsshell. Übergeben Sie die `-f|--framework`-Option, um die Version auszuwählen. Im folgenden Beispiel wird die App für die .NET Core-Version 3.1 erstellt:
  
  ```dotnetcli
  dotnet new blazorwasm -o MyBlazorPwa --pwa -f netcoreapp3.1
  ```
  
  Im vorherigen Befehl erstellt die `-o|--output`-Option einen neuen Ordner für die App namens `MyBlazorPwa`.

* Navigieren Sie zum GitHub-Repository für ASP.NET Core unter der folgenden URL, die zur Verweisquelle und den Ressourcen für das 3.1-Release führt:

  [dotnet/aspnetcore (Release 3.1): Blazor WebAssembly-Projektvorlagenordner `wwwroot`](https://github.com/dotnet/aspnetcore/tree/release/3.1/src/ProjectTemplates/ComponentsWebAssembly.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

  > [!NOTE]
  > Die URL für die Blazor WebAssembly-Projektvorlage wurde nach dem Release von ASP.NET Core 3.1 geändert. Verweisressourcen für Version 5.0 und höher stehen unter der folgenden URL zur Verfügung:
  >
  > [dotnet/aspnetcore (Release 5.0): Blazor WebAssembly-Projektvorlagenordner `wwwroot`](https://github.com/dotnet/aspnetcore/tree/release/5.0/src/ProjectTemplates/Web.ProjectTemplates/content/ComponentsWebAssembly-CSharp/Client/wwwroot)

::: moniker-end

Kopieren Sie aus dem `wwwroot`-Quellordner entweder in der App, die Sie erstellt haben, oder über die Verweisressourcen im GitHub-Repository `dotnet/aspnetcore` die folgenden Dateien in den `wwwroot`-Ordner der App:

* `icon-512.png`
* `manifest.json`
* `service-worker.js`
* `service-worker.published.js`

Führen Sie in der `wwwroot/index.html`-Datei der App die folgenden Schritte aus:

* Fügen Sie `<link>`-Elemente für das Manifest und das App-Symbol hinzu:

  ```html
  <link href="manifest.json" rel="manifest" />
  <link rel="apple-touch-icon" sizes="512x512" href="icon-512.png" />
  ```

* Fügen Sie das folgende `<script>`-Tag innerhalb des schließenden `</body>`-Tags unmittelbar nach dem `blazor.webassembly.js`-Skripttag hinzu:

  ```html
      ...
      <script>navigator.serviceWorker.register('service-worker.js');</script>
  </body>
  ```

## <a name="installation-and-app-manifest"></a>Installation und App-Manifest

Wenn Benutzer eine App aufrufen, die mithilfe der PWA-Vorlage erstellt wurde, können sie die App im Startmenü, Dock oder Startbildschirm des Betriebssystems installieren. Die Darstellung dieser Option hängt vom Browser des Benutzers ab. Bei Verwendung eines Chromium-basierten Desktopbrowsers wie Microsoft Edge oder Chrome wird die Schaltfläche **Hinzufügen** in der URL-Leiste angezeigt. Wenn der Benutzer auf **Hinzufügen** klickt, wird ein Dialogfeld zum Bestätigen angezeigt:

![Das Bestätigungsdialogfeld in Google Chrome zeigt dem Benutzer eine Schaltfläche zum Installieren der MyBlazorPwa-App an.](progressive-web-app/_static/image2.png)

Unter iOS können Besucher die PWA mithilfe der Schaltfläche **Teilen** von Safari und der zugehörigen Option **Zum Home-Bildschirm** installieren. In Chrome für Android sollten Benutzer auf die Schaltfläche **Menü** in der oberen rechten Ecke tippen und dann **Zum Startbildschirm hinzufügen** auswählen.

Nach der Installation wird die App in einem eigenen Fenster ohne Adressleiste angezeigt:

![Ausführung der 'MyBlazorPwa'-App in Google Chrome ohne Adressleiste](progressive-web-app/_static/image3.png)

Informationen zum Anpassen des Titels, des Farbschemas, des Symbols oder anderer Details des Fensters finden Sie in der Datei `manifest.json` im Verzeichnis `wwwroot` des Projekts. Das Schema dieser Datei wird durch Webstandards definiert. Weitere Informationen finden Sie unter [MDN Web Docs: Web-App-Manifest](https://developer.mozilla.org/docs/Web/Manifest).

## <a name="offline-support"></a>Offlineunterstützung

Die Offlineausführung von Apps, die mit der PWA-Vorlagenoption erstellt wurden, wird standardmäßig unterstützt. Ein Benutzer muss die App zuerst online aufrufen. Der Browser lädt die erforderlichen Ressourcen für die Offlineausführung automatisch herunter und zwischenspeichert diese.

> [!IMPORTANT]
> Die Entwicklungsunterstützung würde den üblichen Entwicklungszyklus beeinträchtigen, der das Vornehmen von Änderungen und Testen dieser umfasst. Daher ist die Offlineunterstützung nur für *veröffentlichte* Apps aktiviert. 

> [!WARNING]
> Wenn Sie eine offlinefähige PWA verteilen möchten, müssen Sie [einige wichtige Warnungen und Einschränkungen berücksichtigen](#caveats-for-offline-pwas). Diese Szenarios gelten allgemein für Offline-PWAs und sind nicht auf Blazor beschränkt. Sie müssen diese Einschränkungen lesen und verstehen, bevor Sie Vermutungen über die Funktionsweise der offlinefähigen App anstellen.

So erfahren Sie, wie die Offlineunterstützung funktioniert:

1. Veröffentlichen Sie die App. Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/index#publish-the-app>.
1. Stellen Sie die App auf einem Server bereit, der HTTPS unterstützt, und greifen Sie auf die App über ihre sichere HTTPS-Adresse in einem Browser zu.
1. Öffnen Sie die Entwicklertools des Browsers, und überprüfen Sie, ob ein *Service Worker* für den Host auf der Registerkarte **Anwendung** registriert ist:

   ![Auf der Registerkarte „Anwendung“ der Entwicklertools in Google Chrome wird ein aktivierter und ausgeführter Service Worker angezeigt.](progressive-web-app/_static/image4.png)

1. Laden Sie die Seite neu, und überprüfen Sie die Registerkarte **Netzwerk**. **Service Worker** oder **Arbeitsspeichercache** wird als Quelle für alle Ressourcen der Seite aufgeführt:

   ![Auf der Registerkarte „Netzwerk“ der Entwicklertools werden die Quellen für alle Ressourcen der Seite angezeigt.](progressive-web-app/_static/image5.png)

1. Führen Sie einen der folgenden Schritte aus, um zu überprüfen, ob der Browser Netzwerkzugriff zum Laden der App benötigt:

   * Fahren Sie den Webserver herunter, und überprüfen Sie, wie die App normalerweise funktioniert, dazu gehört auch das Neuladen der Seite. Ebenso funktioniert die App weiterhin normal, wenn eine langsame Netzwerkverbindung vorliegt.
   * Weisen Sie den Browser auf der Registerkarte **Netzwerk** dazu an, den Offlinemodus zu simulieren:

   ![Registerkarte „Netzwerk“ in den Entwicklertools von Google Chrome mit der Änderung des Dropdownfelds für den Browsermodus von „Online“ in „Offline“](progressive-web-app/_static/image6.png)

Die Offlineunterstützung mit einem Service Worker ist ein Webstandard, der nicht spezifisch für Blazor gilt. Weitere Informationen über Service Worker finden Sie unter [MDN Web Docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API). Weitere Informationen über gängige Nutzungsmuster für Service Worker finden Sie unter [Google Web: The Service Worker Lifecycle (Google Web: Service Worker-Lebenszyklus)](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle).

Die PWA-Vorlage von Blazor erzeugt zwei Service-Worker-Dateien:

* `wwwroot/service-worker.js`, die während der Entwicklung verwendet wird.
* `wwwroot/service-worker.published.js`, die nach der Veröffentlichung der App verwendet wird.

Sie können den folgenden Ansatz verwenden, um die Logik zwischen den zwei Service Worker-Dateien freizugeben:

* Fügen Sie eine dritte JavaScript-Datei hinzu, die die gemeinsame Logik enthält.
* Verwenden Sie [`self.importScripts`](https://developer.mozilla.org/docs/Web/API/WorkerGlobalScope/importScripts), um die Logik in beide Service Worker-Dateien zu laden.

### <a name="cache-first-fetch-strategy"></a>Cache-First-Abrufstrategie

Der integrierte Service Worker `service-worker.published.js` löst Anforderungen mit einer *Cache-First*-Strategie auf. Das bedeutet, dass der Service Worker bevorzugt, zwischengespeicherte Inhalte zurückzugeben, unabhängig davon, ob der Benutzer über Netzwerkzugriff verfügt oder ob neuere Inhalte auf dem Server verfügbar sind.

Die Cache-First-Strategie hat folgende Vorteile:

* **Die Zuverlässigkeit wird sichergestellt.** Der Netzwerkzugriff ist kein boolescher Zustand. Ein Benutzer ist nicht einfach online oder offline:

  * Das Gerät des Benutzers geht möglicherweise davon aus, dass es online ist, jedoch kann das Netzwerk so langsam sein, dass es unpraktisch ist, auf es zu warten.
  * Das Netzwerk könnte ungültige Ergebnisse für bestimmte URLs zurückgeben, z. B. wenn ein Captive-WLAN-Portal vorliegt, das bestimmte Anforderungen derzeit blockiert oder umleitet.
  
  Aus diesem Grund ist die `navigator.onLine`-API des Browsers nicht zuverlässig, und Sie sollten nicht auf sie vertrauen.

* **Die Richtigkeit wird sichergestellt.** Beim Erstellen eines Caches für Offlineressourcen stellt der Service Worker mithilfe von Inhaltshashs sicher, dass er zu einem bestimmten Zeitpunkt eine vollständige und in sich konsistente Momentaufnahme der Ressourcen abgerufen hat. Dieser Cache wird dann als atomische Einheit verwendet. Es macht keinen Sinn, neue Ressourcen vom Netzwerk anzufordern, da nur die bereits zwischengespeicherten Versionen erforderlich sind. Alle anderen Strategien sind mit dem Risiko von Inkonsistenz und Inkompatibilität behaftet (wenn z. B. versucht wird, Versionen von .NET-Assemblys zu verwenden, die nicht gemeinsam kompiliert wurden).

### <a name="background-updates"></a>Hintergrundupdates

Sie können sich das Verhalten einer Offline-First-PWA wie das einer mobilen App vorstellen, die installiert werden kann. Die App wird unabhängig von der Netzwerkkonnektivität immer sofort gestartet, die installierte App-Logik stammt jedoch aus einer Zeitpunkt-Momentaufnahme, die möglicherweise nicht die neueste Version ist.

Mit der Blazor-PWA-Vorlage werden Apps erstellt, die immer automatisch versuchen, sich im Hintergrund zu aktualisieren, wenn sie vom Benutzer besucht werden und eine funktionsfähige Netzwerkverbindung besteht. Dies geschieht auf folgende Weise:

* Das Projekt generiert während der Kompilierung ein *Service Worker-Assetmanifest*. Das wird standardmäßig `service-worker-assets.js` genannt. Das Manifest führt alle statischen Ressourcen auf, die die App benötigt, um offline zu funktionieren, z. B. .NET-Assemblys, JavaScript-Dateien und CSS, einschließlich ihrer Inhaltshashs. Diese Ressourcenliste wird vom Service Worker geladen, damit er weiß, welche Ressourcen zwischengespeichert werden sollen.
* Jedes Mal, wenn der Benutzer die App aufruft, fordert der Browser die Dateien `service-worker.js` und `service-worker-assets.js` im Hintergrund erneut an. Die Dateien werden Byte für Byte mit dem vorhandenen installierten Service Worker verglichen. Wenn der Server geänderte Inhalte für eine dieser Dateien zurückgibt, versucht der Service Worker, eine neue Version zu installieren.
* Bei der Installation der neuen Version erstellt der Service Worker einen neuen separaten Cache für Offlineressourcen und füllt diesem mit den Ressourcen auf, die in der Datei `service-worker-assets.js` aufgeführt sind. Diese Logik ist in der Funktion `onInstall` in `service-worker.published.js` implementiert.
* Der Prozess wird erfolgreich abgeschlossen, wenn alle Ressourcen ohne Fehler geladen werden und alle Inhaltshashs übereinstimmen. Nach erfolgreicher Installation geht der neue Service Worker in den Zustand *waiting for activation* (Auf Aktivierung warten) über. Sobald der Benutzer die App schließt (keine Registerkarten oder Fenster der App verbleiben), wird der neue Service Worker *aktiv* und für nachfolgende Aufrufe der App verwendet. Der alte Service Worker und der zugehörige Cache werden gelöscht.
* Wenn der Prozess nicht erfolgreich abgeschlossen wird, wird die neue Service Worker-Instanz verworfen. Der Updateprozess wird dann beim nächsten Aufruf durch den Benutzer wieder versucht. Dann sollte der Client im Idealfall über eine bessere Netzwerkverbindung verfügen, um die Anforderungen zu erfüllen.

Sie können diesen Prozess anpassen, indem Sie die Service Worker-Logik bearbeiten. Keines der zuvor beschriebenen Verhalten gilt spezifisch für Blazor, es handelt sich dabei lediglich um die Standardfunktionsweise der PWA-Vorlagenoption. Weitere Informationen finden Sie unter [MDN Web Docs: Service Worker API](https://developer.mozilla.org/docs/Web/API/Service_Worker_API).

### <a name="how-requests-are-resolved"></a>Auflösen von Anforderungen

Wie im Abschnitt [Cache-First-Abrufstrategie](#cache-first-fetch-strategy) beschrieben verwendet der Standard-Service Worker eine *Cache-First-Strategie*, d. h., er versucht zwischengespeicherte Inhalte bereitzustellen, wenn diese verfügbar sind. Wenn für eine bestimmte URL kein Inhalt zwischengespeichert ist, z. B., wenn Daten von einer Back-End-API angefordert werden, greif der Service Worker auf eine herkömmliche Netzwerkanforderung zurück. Die Netzwerkanforderung wird erfolgreich durchgeführt, wenn der Server erreichbar ist. Diese Logik ist in der `onFetch`-Funktion in der Datei `service-worker.published.js` implementiert.

Wenn die Razor-Komponenten der App Daten von Back-End-APIs anfordern müssen und Sie ein benutzerfreundliches Verfahren für fehlgeschlagene Anforderungen bei nicht verfügbarer Netzwerkverbindung anbieten möchten, können Sie die Logik innerhalb der App-Komponenten implementieren. Schließen Sie z. B. <xref:System.Net.Http.HttpClient>-Anforderungen in `try/catch` ein.

### <a name="support-server-rendered-pages"></a>Unterstützung von auf dem Server gerenderten Seiten

Beachten Sie, was geschieht, wenn der Benutzer zuerst eine URL (z. B. `/counter`) oder einen anderen Deep-Link in der App aufruft. In diesen Fällen sollen keine Inhalte zurückgegeben werden, die als `/counter` zwischengespeichert sind. Stattdessen muss der Browser als `/index.html` zwischengespeicherte Inhalte laden, um die Blazor WebAssembly-App zu starten. Diese anfänglichen Anforderungen werden als *Navigationsanforderungen* bezeichnet. Außerdem gibt es:

* `subresource`-Anforderungen für Bilder, Stylesheets oder andere Dateien.
* `fetch/XHR`-Anforderungen für API-Daten.

Der Standard-Service-Worker enthält eine spezielle Logik für Navigationsanforderungen. Der Service Worker löst die Anforderungen auf, indem er den zwischengespeicherten Inhalt unabhängig von der angeforderten URL für `/index.html` zurückgibt. Diese Logik ist in der Funktion `onFetch` in `service-worker.published.js` implementiert.

Wenn die App über bestimmte URLs verfügt, die auf dem Server gerendertes HTML zurückgeben müssen, ohne `/index.html` aus dem Cache bereitzustellen, müssen Sie die Logik im Service Worker bearbeiten. Wenn alle URLs, die `/Identity/` enthalten, als reguläre Onlineanforderungen an den Server behandelt werden müssen, bearbeiten Sie die `onFetch`-Logik von `service-worker.published.js`. Suchen Sie den folgenden Code:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate';
```

Ändern Sie den Code wie folgt:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/');
```

Wenn Sie dies nicht tun, fängt der Service Worker unabhängig von der Netzwerkkonnektivität Anforderungen für diese URLs ab und löst sie mithilfe von `/index.html` auf.

Fügen Sie der Überprüfung weitere Endpunkte für externe Authentifizierungsanbieter hinzu. Im folgenden Beispiel wird der Überprüfung `/signin-google` für die Google-Authentifizierung hinzugefügt:

```javascript
const shouldServeIndexHtml = event.request.mode === 'navigate'
  && !event.request.url.includes('/Identity/')
  && !event.request.url.includes('/signin-google');
```

Für die Entwicklungsumgebung ist keine Aktion erforderlich. Dort werden Inhalte immer aus dem Netzwerk abgerufen.

### <a name="control-asset-caching"></a>Steuern der Zwischenspeicherung von Ressourcen

Wenn Ihr Projekt die MSBuild-Eigenschaft `ServiceWorkerAssetsManifest` definiert, erstellen die Buildtools von Blazor ein Service Worker-Assetmanifest mit dem angegebenen Namen. Die PWA-Standardvorlage erzeugt eine Projektdatei, die die folgende Eigenschaft enthält:

```xml
<ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
```

Die Datei wird in das Ausgabeverzeichnis `wwwroot` eingefügt, sodass sie vom Browser durch Anfordern von `/service-worker-assets.js` abgerufen werden kann. Um den Inhalt dieser Datei anzuzeigen, öffnen Sie `/bin/Debug/{TARGET FRAMEWORK}/wwwroot/service-worker-assets.js` in einem Text-Editor. Bearbeiten Sie die Datei jedoch nicht, da sie mit jedem Build neu generiert wird.

Standardmäßig wird in diesem Manifest Folgendes aufgeführt:

* Alle von Blazor verwalteten Ressourcen, z. B. .NET-Assemblys und die .NET-WebAssembly-Runtimedateien, die für die Offlineausführung erforderlich sind
* Alle Ressourcen für die Veröffentlichung im `wwwroot`-Verzeichnis, z. B. Bilder, Stylesheets und JavaScript-Dateien, einschließlich der statischen Webressourcen, die von externen Projekten und NuGet-Paketen bereitgestellt werden.

Sie können steuern, welche dieser Ressourcen vom Service Worker abgerufen und zwischengespeichert werden, indem Sie die Logik in `onInstall` in `service-worker.published.js` bearbeiten. Der Service Worker ruft Dateien standardmäßig entsprechend der üblichen Erweiterungen für Webdateinamen ab und speichert diese zwischen, z. B. `.html`, `.css`, `.js` und `.wasm`, zuzüglich Dateitypen, die für die Blazor WebAssembly (`.dll`, `.pdb`) typisch sind.

Definieren Sie zusätzliche `ItemGroup`-MSBuild-Einträge wie im folgenden Beispiel gezeigt, um zusätzliche Ressourcen einzufügen, die nicht im `wwwroot`-Verzeichnis der App vorhanden sind:

```xml
<ItemGroup>
  <ServiceWorkerAssetsManifestItem Include="MyDirectory\AnotherFile.json"
    RelativePath="MyDirectory\AnotherFile.json" AssetUrl="files/AnotherFile.json" />
</ItemGroup>
```

Die `AssetUrl`-Metadaten geben die zur Basis relative URL an, die der Browser beim Abrufen der zwischenzuspeichernden Ressource verwenden soll. Dieser Wert kann unabhängig vom ursprünglichen Quelldateinamen auf dem Datenträger sein.

> [!IMPORTANT]
> Durch das Hinzufügen von `ServiceWorkerAssetsManifestItem` wird die Datei nicht im `wwwroot`-Verzeichnis der App veröffentlicht. Die Veröffentlichungsausgabe muss separat gesteuert werden. Das `ServiceWorkerAssetsManifestItem` bewirkt lediglich, dass im Service-Worker-Assetmanifest ein zusätzlicher Eintrag angezeigt wird.

## <a name="push-notifications"></a>Pushbenachrichtigungen

Eine Blazor WebAssembly-PWA kann wie jede andere PWA Pushbenachrichtigungen von einem Back-End-Server empfangen. Der Server kann jederzeit Pushbenachrichtigungen senden, auch wenn der Benutzer die App nicht aktiv verwendet. Beispielsweise können Pushbenachrichtigungen gesendet werden, wenn ein anderer Benutzer eine relevante Aktion ausführt.

Der Mechanismus zum Senden einer Pushbenachrichtigung ist vollständig unabhängig von der Blazor WebAssembly, da er vom Back-End-Server implementiert wird, und dieser kann eine beliebige Technologie verwenden. Wenn Sie Pushbenachrichtigungen von einem ASP.NET Core-Server senden möchten, sollten Sie [ein ähnliches Verfahren wie beim Blazing Pizza-Workshop](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#sending-push-notifications) verwenden.

Der Mechanismus zum Empfangen und Anzeigen einer Pushbenachrichtigung auf dem Client hängt nicht von der Blazor WebAssembly ab, da er in der JavaScript-Datei vom Service Worker implementiert ist. Ein Beispiel finden Sie im [Verfahren des Blazing Pizza-Workshops](https://github.com/dotnet-presentations/blazor-workshop/blob/master/docs/09-progressive-web-app.md#displaying-notifications).

## <a name="caveats-for-offline-pwas"></a>Einschränkungen für Offline-PWAs

Nicht alle Apps sollten die Offlineverwendung unterstützen. Mit der Offlineunterstützung wird die Komplexität deutlich erhöht und sie ist nicht immer für die erforderlichen Anwendungsfälle relevant.

Die Offlineunterstützung ist in der Regel nur in folgenden Situationen relevant:

* Wenn der primäre Datenspeicher für den Browser lokal ist. Der Ansatz eignet sich beispielsweise für Apps mit einer Benutzeroberfläche für ein [IoT-Gerät](https://en.wikipedia.org/wiki/Internet_of_things), das Daten in `localStorage` oder [IndexedDB](https://developer.mozilla.org/docs/Web/API/IndexedDB_API) speichert.
* Wenn die App einen beträchtlichen Aufwand tätigen muss, um die Daten der Back-End-API für die einzelnen Benutzer abzurufen und zwischenzuspeichern, damit sie die Daten offline durchsuchen können. Wenn die App die Bearbeitung unterstützen muss, muss ein System für die Nachverfolgung von Änderungen und die Synchronisierung von Daten mit dem Back-End erstellt werden.
* Wenn Sie sicherstellen möchten, dass die App unabhängig von den Netzwerkbedingungen sofort geladen wird. Implementieren Sie eine geeignete Benutzeroberfläche für Back-End-API-Anforderungen, um den Fortschritt von Anforderungen zu veranschaulichen und ein ordnungsgemäßes Verhalten zu implementieren, wenn Anforderungen aufgrund von Nichtverfügbarkeit des Netzwerks fehlschlagen.

Offlinefähige PWAs müssen zudem eine Reihe zusätzlicher Komplikationen bewältigen. Entwickler sollten sich in den folgenden Abschnitten mit den Nachteilen vertraut machen.

### <a name="offline-support-only-when-published"></a>Offlineunterstützung nur bei Veröffentlichung

Während der Entwicklung möchten Sie Änderungen in der Regel sofort im Browser sehen, ohne einen Updateprozess im Hintergrund zu durchlaufen. Daher aktiviert die PWA-Vorlage von Blazor die Offlineunterstützung nur, wenn die App veröffentlicht wird.

Beim Erstellen eine offlinefähigen App reicht es nicht aus, die App in der Entwicklungsumgebung zu testen. Sie müssen die App im veröffentlichten Zustand testen, um zu verstehen, wie sie auf verschiedene Netzwerkbedingungen reagiert.

### <a name="update-completion-after-user-navigation-away-from-app"></a>Abschluss des Updates, nachdem der Benutzer die App verlassen hat

Updates werden erst abgeschlossen, wenn der Benutzer die App auf allen Registerkarten verlassen hat. Nach der Bereitstellung eines Updates für die App, ruft der Browser die aktualisierten Service Worker-Dateien wie im Abschnitt [Hintergrundupdates](#background-updates) beschrieben ab, um den Updateprozess einzuleiten.

Viele Entwickler wissen nicht, dass das Update nach seinem Abschluss **erst dann** wirksam wird, wenn die Anwendung auf allen Registerkarten vom Benutzer verlassen wurde. Es reicht **nicht** aus, die Registerkarte zu aktualisieren, auf der die App angezeigt wird, selbst wenn dies die einzige Registerkarte ist, auf der sie angezeigt wird. Bis Ihre App vollständig geschlossen wurde, weist der neue Service Worker den Zustand *waiting to activate* (Auf Aktivierung warten) auf. **Dies ist nicht auf Blazor beschränkt, sondern ein Standardverhalten von Webplattformen.**

Dies bereitet häufig Entwicklern Probleme, die versuchen, Updates des Service Worker oder von offline zwischengespeicherten Ressourcen zu testen. Wenn Sie die Entwicklertools Ihres Browsers überprüfen, wird möglicherweise Folgendes angezeigt:

![Registerkarte „Anwendung“ von Google Chrome zeigt den Zustand „waiting to activate“ (Auf Aktivierung warten) für den Service Worker der App an.](progressive-web-app/_static/image7.png)

Solange die Liste der „Clients“, bei denen es sich um Registerkarten oder Fenster handelt, die Ihre App anzeigen, nicht leer ist, wartet der Worker weiter. Service Worker verfügen über diese Funktionsweise, um Konsistenz zu garantieren. Konsistenz bedeutet, dass alle Ressourcen aus demselben atomischen Cache abgerufen werden.

Wenn Sie Änderungen testen, können Sie einfach auf den im obigen Screenshot gezeigten Link „skipWaiting“ klicken und dann die Seite neu laden. Sie können dies für alle Benutzer automatisieren, indem Sie den Service Worker so programmieren, dass [bei einem Update die Wartephase übersprungen und der Service Worker sofort aktiviert wird](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#skip_the_waiting_phase). Wenn Sie die Wartephase überspringen, kann nicht garantiert werden, dass Ressourcen immer konsistent aus derselben Cache-Instanz abgerufen werden.

### <a name="users-may-run-any-historical-version-of-the-app"></a>Benutzer können jede frühere Version der App ausführen.

Webentwickler erwarten gewohnheitsmäßig, dass Benutzer nur die zuletzt bereitgestellte Version ihrer Web-App ausführen, da dies im herkömmlichen Webverteilungsmodell üblich ist. Eine Offline-First-PWA ähnelt jedoch eher einer nativen mobilen App, bei der die Benutzer nicht unbedingt die neueste Version ausführen.

Wie im Abschnitt [Hintergrundupdates](#background-updates) beschrieben, **verwendet jeder vorhandene Benutzer weiterhin eine vorherige Version, bis sie die App wieder aufrufen**, nachdem Sie ein Update für Ihre App bereitgestellt haben, da das Update im Hintergrund durchgeführt und erst aktiviert wird, wenn der Benutzer die App geschlossen hat. Außerdem ist die vorherige verwendete Version nicht unbedingt die Version, die Sie zuletzt bereitgestellt haben. Je nachdem, wann der Benutzer zuletzt ein Update durchgeführt hat, kann es sich bei der vorherigen Version um eine *beliebige* historische Version handeln.

Dies kann ein Problem darstellen, wenn die Front-End- und Back-End-Teile der App eine Vereinbarung über das Schema für API-Anforderungen erfordern. Sie dürfen keine API-Schemaänderungen bereitstellen, die nicht abwärtskompatibel sind, solange Sie sich nicht vergewissert haben, dass alle Benutzer ein Upgrade durchgeführt haben. Alternativ müssen Sie die Verwendung nicht kompatibler, alter Versionen der App für Benutzer verhindern. Diese Anforderung gilt auch für native mobile Apps. Wenn Sie einen Breaking Change in Server-APIs bereitstellen, wird die Client-App für Benutzer beschädigt, die noch kein Update durchgeführt haben.

Stellen Sie nach Möglichkeit keine Breaking Changes für die Back-End-APIs bereit. Wenn sich dies nicht vermeiden lässt, sollten Sie mit [Service Worker-Standard-APIs wie ServiceWorkerRegistration](https://developer.mozilla.org/docs/Web/API/ServiceWorkerRegistration) bestimmen, ob die App auf dem neuesten Stand ist. Wenn das nicht der Fall ist, sollten Sie die Verwendung verhindern.

### <a name="interference-with-server-rendered-pages"></a>Störungen bei auf dem Server gerenderten Seiten

Bearbeiten Sie die Logik in Ihrem Service Worker wie im Abschnitt [Unterstützung von auf dem Server gerenderten Seiten](#support-server-rendered-pages) beschrieben, wenn Sie das Verhalten vom Service Worker, `/index.html`-Inhalte für alle Navigationsanforderungen zurückzugeben, umgehen möchten.

### <a name="all-service-worker-asset-manifest-contents-are-cached-by-default"></a>Alle Inhalte des Service-Worker-Assetmanifests werden standardmäßig zwischengespeichert.

Die Datei `service-worker-assets.js` wird wie im Abschnitt [Steuern der Zwischenspeicherung von Ressourcen](#control-asset-caching) beschrieben während des Buildvorgangs generiert und führt alle Ressourcen auf, die der Service Worker abrufen und zwischenspeichern soll.

Da in dieser Liste standardmäßig alles enthalten ist, was an `wwwroot` ausgegeben wurde (einschließlich Inhalte, die durch externe Pakete und Projekte bereitgestellt wurden), müssen Sie darauf achten, dass Sie nicht zu viele Inhalte einfügen. Wenn das Verzeichnis `wwwroot` Millionen Bilder enthält, versucht der Service Worker, sie alle abzurufen und zwischenzuspeichern. Dabei wird eine übermäßige Bandbreite beansprucht, und der Vorgang wird wahrscheinlich nicht erfolgreich abgeschlossen.

Sie können beliebige Logik implementieren, um zu steuern, welche Teilmenge der Inhalte des Manifests abgerufen und zwischengespeichert werden, indem Sie die Funktion `onInstall` in `service-worker.published.js` bearbeiten.

### <a name="interaction-with-authentication"></a>Interaktion mit Authentifizierung

Die PWA-Vorlage kann in Verbindung mit der Authentifizierung verwendet werden. Eine offlinefähige PWA kann auch die Authentifizierung unterstützen, wenn der Benutzer über eine anfängliche Netzwerkkonnektivität verfügt.

Wenn ein Benutzer über keine Netzwerkkonnektivität verfügt, kann er sich nicht authentifizieren und keine Zugriffstoken abrufen. Versuche, die Anmeldeseite ohne Netzwerkzugriff zu besuchen, führen standardmäßig zu Netzwerkfehlermeldungen. Sie müssen einen Benutzeroberflächenflow entwerfen, den Benutzer für nützliche Aktionen offline ausführen können, ohne dass sie versuchen müssen, sich zu authentifizieren oder Zugriffstoken abzurufen. Alternativ können Sie die App so entwerfen, dass sie ordnungsgemäß fehlschlägt, wenn das Netzwerk nicht verfügbar ist. Wenn die App nicht für die Verarbeitung dieser Szenarios entwickelt werden kann, sollten Sie die Offlineunterstützung nicht aktivieren.

Folgendes trifft zu, wenn eine App, die für die Online- und Offlinenutzung vorgesehen ist, wieder online geschaltet wird:

* Die App muss möglicherweise ein neues Zugriffstoken bereitstellen.
* Die App muss erkennen, ob ein anderer Benutzer beim Dienst angemeldet ist, sodass sie Vorgänge auf das Konto eines Benutzers anwenden kann, die vorgenommen wurden, als die App offline war.

So erstellen Sie eine PWA-Offline-App, die mit der Authentifizierung interagiert:

* Ersetzen Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>-Klasse durch eine Zuordnungsinstanz, die den zuletzt angemeldeten Benutzer speichert und diesen gespeicherten Benutzer verwendet, wenn die App offline ist.
* Reihen Sie Vorgänge in die Warteschlange ein, während die App offline ist, und wenden Sie diese an, wenn die App wieder online ist.
* Löschen Sie bei der Abmeldung den gespeicherten Benutzer.

Die Beispiel-App [`CarChecker`](https://github.com/SteveSandersonMS/CarChecker) veranschaulicht die obigen Ansätze. Sehen Sie sich die folgenden Teile der App an:

* `OfflineAccountClaimsPrincipalFactory` (`Client/Data/OfflineAccountClaimsPrincipalFactory.cs`)
* `LocalVehiclesStore` (`Client/Data/LocalVehiclesStore.cs`)
* `LoginStatus`-Komponente (`Client/Shared/LoginStatus.razor`)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [PowerShell-Skript für die Behebung von Problemen bei der Integrität](xref:blazor/host-and-deploy/webassembly#troubleshoot-integrity-powershell-script)
* [Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
