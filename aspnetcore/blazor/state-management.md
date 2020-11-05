---
title: Blazor-Zustandsverwaltung in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie den Zustand in Blazor Server-Apps beibehalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/state-management
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 1769ddbb95c9ffe373e916c885e411adc3d4c65b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054995"
---
# <a name="aspnet-core-no-locblazor-state-management"></a>Blazor-Zustandsverwaltung in ASP.NET Core

Von [Steve Sanderson](https://github.com/SteveSandersonMS) und [Luke Latham](https://github.com/guardrex)

::: zone pivot="webassembly"

Der Benutzerzustand, der in einer Blazor WebAssembly-App erstellt wird, wird im Arbeitsspeicher des Browsers gespeichert.

Beispiele für den Benutzerzustand im Arbeitsspeicher des Browsers sind:

* Die Hierarchie der Komponenteninstanzen und deren neueste Renderausgabe in der gerenderten Benutzeroberfläche.
* Die Werte der Felder und Eigenschaften in Komponenteninstanzen.
* Daten, die in [DI](xref:fundamentals/dependency-injection)-Dienstinstanzen (Dependency Injection, Abhängigkeitsinjektion) gespeichert sind.
* Werte, die durch [JavaScript-Interop](xref:blazor/call-javascript-from-dotnet)-Aufrufe festgelegt werden.

Wenn ein Benutzer seinen Browser schließt und erneut öffnet oder die Seite erneut lädt, geht der Benutzerzustand im Arbeitsspeicher des Browsers verloren.

## <a name="persist-state-across-browser-sessions"></a>Persistentes Speichern des Zustands über Browsersitzungen hinweg

Im Allgemeinen gilt: Die Beibehaltung des Zustands über mehrere Browsersitzungen hinweg erfolgt in Szenarien, in denen Benutzer aktiv Daten erstellen und nicht nur Daten lesen, die bereits vorhanden sind.

Um den Zustand über Browsersitzungen hinweg beizubehalten, muss die App die Daten an einem anderen Speicherort als im Arbeitsspeicher des Browsers persistent speichern. Die Zustandspersistenz erfolgt nicht automatisch. Sie müssen bei der Entwicklung der App Schritte ausführen, um zustandsbehaftete Datenpersistenz zu implementieren.

Die Datenpersistenz ist in der Regel nur für Zustände mit hohem Wert erforderlich, die von Benutzern mit großem Aufwand erstellt wurden. In den folgenden Beispielen werden durch die Beibehaltung des Zustands Zeit- oder Hilfsmitteleinsparungen in kommerziellen Aktivitäten erzielt:

* Webformulare mit mehreren Schritten: Es ist für den Benutzer zeitaufwändig, Daten für mehrere abgeschlossene Schritte eines mehrstufigen Webformulars wiederholt einzugeben, wenn der Zustand nicht mehr vorhanden ist. Der Benutzer verliert in diesem Szenario seinen Zustand, wenn er vom Formular weg navigiert und später zurückkehrt.
* Warenkörbe: Kommerziell wichtige Komponenten einer App, die potenzielle Umsätze ermöglichen, können beibehalten werden. Ein Benutzer, der seinen Zustand verliert und dessen Warenkorb dadurch gelöscht wird, kann weniger Produkte oder Dienste kaufen, wenn er zu einem späteren Zeitpunkt zur Website zurückkehrt.

Eine App kann nur den *App-Zustand* beibehalten. Benutzeroberflächen wie Komponenteninstanzen und deren Renderingstrukturen können nicht beibehalten werden. Komponenten und Renderingstrukturen sind in der Regel nicht serialisierbar. Um den Benutzeroberflächenzustand (z. B. von den erweiterten Knoten einer Strukturansicht) zu erhalten, muss die App über benutzerdefinierten Code verfügen, um das Verhalten des Benutzeroberflächenzustands als serialisierbaren App-Zustand modellieren zu können.

## <a name="where-to-persist-state"></a>Speicherort des Zustands

Es gibt allgemeine Speicherorte für die Beibehaltung des Zustands:

* [Serverseitige Speicherung](#server-side-storage)
* [URL](#url)
* [Browserspeicherung](#browser-storage)
* [Arbeitsspeicherinterner Zustandscontainer des Diensts](#in-memory-state-container-service)

### <a name="server-side-storage"></a>Serverseitige Speicherung

Für permanente Datenspeicherung, die mehrere Benutzer und Geräte umfasst, kann die App unabhängigen serverseitigen Speicher verwenden, auf den über eine Web-API zugegriffen wird. Folgende Optionen sind verfügbar:

* Blob-Speicher
* Schlüssel-Wert-Speicher
* Relationale Datenbank
* Table Storage

Nachdem die Daten gespeichert wurden, wird der Zustand des Benutzers beibehalten und ist in jeder neuen Browsersitzung verfügbar.

Da Blazor WebAssembly-Apps vollständig im Browser des Benutzers ausgeführt werden, benötigen sie zusätzliche Maßnahmen für den Zugriff auf sichere externe Systeme, z. B. auf Speicherdienste und Datenbanken. Blazor WebAssembly-Apps werden auf die gleiche Weise gesichert wie Single-Page-Anwendungen (SPAs). In der Regel authentifiziert eine App einen Benutzer über [OAuth](https://oauth.net)/[OpenID Connect (OIDC)](https://openid.net/connect/) und interagiert dann über Web-API-Aufrufe einer serverseitigen App mit Speicherdiensten und Datenbanken. Die serverseitige Anwendung vermittelt die Übertragung von Daten zwischen der Blazor WebAssembly-Anwendung und dem Speicherdienst oder der Datenbank. Die Blazor WebAssembly-App unterhält eine kurzlebige Verbindung mit der serverseitigen App, während die serverseitige App über eine permanente Verbindung mit dem Speicher verfügt.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>
* Blazor *Sicherheit und Identity* -Artikel

Weitere Informationen zu den Azure-Datenspeicheroptionen finden Sie hier:

* [Azure-Datenbanken](https://azure.microsoft.com/product-categories/databases/)
* [Azure Storage-Dokumentation](/azure/storage/)

### <a name="url"></a>URL

Modellieren Sie vorübergehende Daten, die den Navigationszustand darstellen, als Teil der URL. Beispiele für in der URL modellierte Benutzerzustände:

* Die ID einer angezeigten Entität.
* Die aktuelle Seitenzahl in einem ausgelagerten Raster.

Der Inhalt der Adressleiste des Browsers wird beibehalten, wenn der Benutzer die Seite manuell erneut lädt.

Weitere Informationen zum Definieren von URL-Mustern mit der [`@page`](xref:mvc/views/razor#page)-Direktive finden Sie unter <xref:blazor/fundamentals/routing>.

### <a name="browser-storage"></a>Browserspeicherung

Für vorübergehende Daten, die der Benutzer aktiv erstellt, sind die [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage)- und [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage)-Sammlungen des Browsers ein häufig verwendeter Speicherort:

* `localStorage` bezieht sich auf das Fenster des Browsers. Wenn der Benutzer die Seite noch mal lädt oder den Browser schließt und erneut öffnet, wird der Zustand beibehalten. Wenn der Benutzer mehrere Browserregisterkarten öffnet, wird der Zustand auf allen Registerkarten freigegeben. Daten bleiben in `localStorage` so lange beibehalten, bis sie explizit gelöscht werden.
* `sessionStorage` bezieht sich auf die Registerkarte des Browsers. Wenn der Benutzer die Registerkarte noch mal lädt, wird der Zustand beibehalten. Wenn der Benutzer die Registerkarte oder den Browser schließt, geht der Zustand verloren. Wenn der Benutzer mehrere Browserregisterkarten öffnet, hat jede Registerkarte eine eigene unabhängige Version der Daten.

> [!NOTE]
> `localStorage` und `sessionStorage` können in Blazor WebAssembly-Apps verwendet werden, jedoch nur durch Schreiben von benutzerdefiniertem Code oder Verwenden eines Drittanbieterpakets.

Im Allgemeinen ist `sessionStorage` in der Anwendung sicherer. `sessionStorage` verhindert, dass ein Benutzer mehrere Registerkarten öffnet und auf folgende Probleme stößt:

* Fehler in Zustandsspeicher über mehrere Registerkarten hinweg
* Verwirrendes Verhalten, wenn eine Registerkarte den Zustand anderer Registerkarten überschreibt

`localStorage` ist die bessere Wahl, wenn der Zustand der App auch nach dem Schließen und erneuten Öffnen des Browsers bestehen bleiben muss.

> [!WARNING]
> Benutzer können die Daten anzeigen oder bearbeiten, die in `localStorage` und `sessionStorage` gespeichert sind.

## <a name="in-memory-state-container-service"></a>Arbeitsspeicherinterner Zustandscontainer des Diensts

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

## <a name="additional-resources"></a>Weitere Ressourcen

* [Speichern des App-Zustands vor einem Authentifizierungsvorgang](xref:blazor/security/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
* <xref:blazor/call-web-api>
* <xref:blazor/security/webassembly/index>

::: zone-end

::: zone pivot="server"

Blazor Server ist ein zustandsbehaftetes App-Framework. In den meisten Fällen unterhält die App eine Verbindung mit dem Server. Der Benutzerzustand wird in einer *Verbindung* im Speicher des Servers gespeichert. 

Beispiele für den in einer Verbindung gespeicherten Benutzerzustand sind:

* Die Hierarchie der Komponenteninstanzen und deren neueste Renderausgabe in der gerenderten Benutzeroberfläche.
* Die Werte der Felder und Eigenschaften in Komponenteninstanzen.
* Daten, die in [-Abhängigkeitsinjektion (DI)](xref:fundamentals/dependency-injection)-Dienstinstanzen gespeichert sind, die auf die Verbindung beschränkt sind.

Der Benutzerzustand ist möglicherweise auch in JavaScript-Variablen im Arbeitsspeicher des Browsers enthalten, die über [JavaScript-Interop](xref:blazor/call-javascript-from-dotnet)-Aufrufe festgelegt werden.

Wenn die Netzwerkverbindung vorübergehend getrennt wird, versucht Blazor, den Benutzer nochmals mit der ursprünglichen Verbindung mit seinem ursprünglichen Zustand zu verbinden. Es ist jedoch nicht immer möglich, einen Benutzer noch mal mit der ursprünglichen Verbindung im Arbeitsspeicher des Servers zu verbinden:

* Der Server kann eine getrennte Verbindung nicht dauerhaft beibehalten. Der Server muss eine getrennte Verbindung nach einem Timeout freigeben, oder wenn der Server nicht über genügend Arbeitsspeicher verfügt.
* In Bereitstellungsumgebungen mit mehreren Servern und Lastenausgleich tritt bei einzelnen Servern möglicherweise ein Fehler auf, oder sie werden automatisch entfernt, wenn nicht mehr das gesamte Anforderungsvolumen verarbeitet werden muss. Die ursprünglichen Serververarbeitungsanforderungen für einen Benutzer sind möglicherweise nicht mehr verfügbar, wenn der Benutzer versucht, erneut eine Verbindung herzustellen.
* Der Benutzer kann den Browser schließen und noch mal öffnen oder die Seite erneut laden, wodurch alle Zustände im Arbeitsspeicher des Browsers entfernt werden. So gehen beispielsweise JavaScript-Variablenwerte, die durch JavaScript-Interop-Aufrufe festgelegt wurden, verloren.

Wenn ein Benutzer nicht erneut mit der ursprünglichen Verbindung verbunden werden kann, erhält der Benutzer eine neue Verbindung mit einem leeren Zustand. Dies entspricht dem Schließen und erneuten Öffnen einer Desktop-App.

## <a name="persist-state-across-circuits"></a>Persistentes verbindungsübergreifendes Speichern des Zustands

Im Allgemeinen gilt: Die Beibehaltung des Zustands über Verbindungen hinweg erfolgt in Szenarien, in denen Benutzer aktiv Daten erstellen und nicht nur Daten lesen, die bereits vorhanden sind.

Um den Zustand über Verbindungen hinweg beizubehalten, muss die App die Daten an einem anderen Speicherort als im Arbeitsspeicher des Servers persistent speichern. Die Zustandspersistenz erfolgt nicht automatisch. Sie müssen bei der Entwicklung der App Schritte ausführen, um zustandsbehaftete Datenpersistenz zu implementieren.

Die Datenpersistenz ist in der Regel nur für Zustände mit hohem Wert erforderlich, die von Benutzern mit großem Aufwand erstellt wurden. In den folgenden Beispielen werden durch die Beibehaltung des Zustands Zeit- oder Hilfsmitteleinsparungen in kommerziellen Aktivitäten erzielt:

* Webformulare mit mehreren Schritten: Es ist für den Benutzer zeitaufwändig, Daten für mehrere abgeschlossene Schritte eines mehrstufigen Webformulars wiederholt einzugeben, wenn der Zustand nicht mehr vorhanden ist. Der Benutzer verliert in diesem Szenario seinen Zustand, wenn er vom Formular weg navigiert und später zurückkehrt.
* Warenkörbe: Kommerziell wichtige Komponenten einer App, die potenzielle Umsätze ermöglichen, können beibehalten werden. Ein Benutzer, der seinen Zustand verliert und dessen Warenkorb dadurch gelöscht wird, kann weniger Produkte oder Dienste kaufen, wenn er zu einem späteren Zeitpunkt zur Website zurückkehrt.

Eine App kann nur den *App-Zustand* beibehalten. Benutzeroberflächen wie Komponenteninstanzen und deren Renderingstrukturen können nicht beibehalten werden. Komponenten und Renderingstrukturen sind in der Regel nicht serialisierbar. Um den Benutzeroberflächenzustand (z. B. von den erweiterten Knoten einer Strukturansicht) zu erhalten, muss die App über benutzerdefinierten Code verfügen, um das Verhalten des Benutzeroberflächenzustands als serialisierbaren App-Zustand modellieren zu können.

## <a name="where-to-persist-state"></a>Speicherort des Zustands

Es gibt allgemeine Speicherorte für die Beibehaltung des Zustands:

* [Serverseitige Speicherung](#server-side-storage)
* [URL](#url)
* [Browserspeicherung](#browser-storage)
* [Arbeitsspeicherinterner Zustandscontainer des Diensts](#in-memory-state-container-service)

### <a name="server-side-storage"></a>Serverseitige Speicherung

Für permanente Datenspeicherung, die mehrere Benutzer und Geräte umfasst, kann die App serverseitigen Speicher verwenden. Folgende Optionen sind verfügbar:

* Blob-Speicher
* Schlüssel-Wert-Speicher
* Relationale Datenbank
* Table Storage

Nachdem die Daten gespeichert wurden, wird der Zustand des Benutzers beibehalten und ist in jeder neuen Verbindung verfügbar.

Weitere Informationen zu den Azure-Datenspeicheroptionen finden Sie hier:

* [Azure-Datenbanken](https://azure.microsoft.com/product-categories/databases/)
* [Azure Storage-Dokumentation](/azure/storage/)

### <a name="url"></a>URL

Modellieren Sie vorübergehende Daten, die den Navigationszustand darstellen, als Teil der URL. Beispiele für in der URL modellierte Benutzerzustände:

* Die ID einer angezeigten Entität.
* Die aktuelle Seitenzahl in einem ausgelagerten Raster.

Der Inhalt der Adressleiste des Browsers wird in folgenden Fällen beibehalten:

* Der Benutzer lädt die Seite nochmals manuell.
* Wenn der Webserver nicht mehr verfügbar ist, ist der Benutzer gezwungen, die Seite nochmals zu laden, damit eine Verbindung mit einem anderen Server hergestellt werden kann.

Weitere Informationen zum Definieren von URL-Mustern mit der [`@page`](xref:mvc/views/razor#page)-Direktive finden Sie unter <xref:blazor/fundamentals/routing>.

### <a name="browser-storage"></a>Browserspeicherung

Für vorübergehende Daten, die der Benutzer aktiv erstellt, sind die [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage)- und [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage)-Sammlungen des Browsers ein häufig verwendeter Speicherort:

* `localStorage` bezieht sich auf das Fenster des Browsers. Wenn der Benutzer die Seite noch mal lädt oder den Browser schließt und erneut öffnet, wird der Zustand beibehalten. Wenn der Benutzer mehrere Browserregisterkarten öffnet, wird der Zustand auf allen Registerkarten freigegeben. Daten bleiben in `localStorage` so lange beibehalten, bis sie explizit gelöscht werden.
* `sessionStorage` bezieht sich auf die Registerkarte des Browsers. Wenn der Benutzer die Registerkarte noch mal lädt, wird der Zustand beibehalten. Wenn der Benutzer die Registerkarte oder den Browser schließt, geht der Zustand verloren. Wenn der Benutzer mehrere Browserregisterkarten öffnet, hat jede Registerkarte eine eigene unabhängige Version der Daten.

Im Allgemeinen ist `sessionStorage` in der Anwendung sicherer. `sessionStorage` verhindert, dass ein Benutzer mehrere Registerkarten öffnet und auf folgende Probleme stößt:

* Fehler in Zustandsspeicher über mehrere Registerkarten hinweg
* Verwirrendes Verhalten, wenn eine Registerkarte den Zustand anderer Registerkarten überschreibt

`localStorage` ist die bessere Wahl, wenn der Zustand der App auch nach dem Schließen und erneuten Öffnen des Browsers bestehen bleiben muss.

Einschränkungen bei der Verwendung des Browserspeichers:

* Ähnlich wie bei der Verwendung einer serverseitigen Datenbank erfolgt das Laden und Speichern von Daten asynchron.
* Im Gegensatz zu einer serverseitigen Datenbank ist der Speicher während des Prerenderings nicht verfügbar, da die angeforderte Seite im Browser während der Prerenderingphase nicht vorhanden ist.
* Es ist ohne Weiteres möglich, einige wenige Kilobyte Daten für Blazor Server-Apps zu speichern. Bei mehr Kilobyte müssen Beeinträchtigungen der Leistung berücksichtigt werden, da die Daten über das Netzwerk geladen und gespeichert werden.
* Benutzer können die Daten anzeigen oder manipulieren. Die [Datenschutzlösung von ASP.NET Core](xref:security/data-protection/introduction) reduziert die damit verbundenen Risiken. Beispielsweise verwendet [ASP.NET Core Protected Browser Storage](#aspnet-core-protected-browser-storage) ASP.NET Core-Datenschutz.

NuGet-Pakete von Drittanbietern stellen APIs für die Verwendung mit `localStorage` und `sessionStorage` bereit. Erwägen Sie die Auswahl eines Pakets, das die [Datenschutzlösung](xref:security/data-protection/introduction) von ASP.NET Core transparent verwendet. Die Datenschutzlösung verschlüsselt gespeicherte Daten und verringert das potenzielle Risiko, dass gespeicherte Daten manipuliert werden. Wenn JSON-serialisierte Daten als Nur-Text gespeichert werden, können Benutzer die Daten mithilfe von Browserentwicklertools anzeigen und auch die gespeicherten Daten ändern. Das Sichern von Daten ist nicht immer problematisch, da die Daten möglicherweise trivial sind. Beispielsweise stellt das Lesen oder Ändern der gespeicherten Farbe eines Benutzeroberflächenelements für den Benutzer oder die Organisation kein ernsthaftes Sicherheitsrisiko dar. Vermeiden Sie, dass Benutzer *sensible Daten* überprüfen oder manipulieren können.

::: moniker range=">= aspnetcore-5.0"

## <a name="aspnet-core-protected-browser-storage"></a>ASP.NET Core Protected Browser Storage

[ASP.NET Core Protected Browser Storage](xref:security/data-protection/introduction) nutzt ASP.NET Core-Datenschutz für [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) und [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).

> [!NOTE]
> Protected Browser Storage basiert auf ASP.NET Core Datenschutz und wird nur für Blazor Server-Apps unterstützt.

### <a name="save-and-load-data-within-a-component"></a>Speichern und Laden von Daten in einer Komponente

Verwenden Sie für jede Komponente, die das Laden oder Speichern von Daten im Browserspeicher erfordert, die [`@inject`](xref:mvc/views/razor#inject)-Anweisung, um eine Instanz von einem der folgenden Komponenten einzufügen:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Die Auswahl hängt davon ab, welchen Browserspeicherort Sie verwenden möchten. Im folgenden Beispiel wird `sessionStorage` verwendet:

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

Die `@using`-Anweisung kann in einer Datei `_Imports.razor` der App anstatt in der Komponente platziert werden. Bei Verwendung der `_Imports.razor`-Datei wird der Namespace für größere Segmente der App oder die gesamte App zur Verfügung gestellt.

Um den `currentCount`-Wert in der `Counter`-Komponente einer App basierend auf der Blazor Server-Projektvorlage beizubehalten, ändern Sie die `IncrementCount`-Methode so, dass `ProtectedSessionStore.SetAsync` verwendet wird:

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Bei größeren, realistischeren Apps ist die Speicherung einzelner Felder ein unwahrscheinliches Szenario. Apps speichern meist ganze Modellobjekte, die komplexe Zustände enthalten. `ProtectedSessionStore` serialisiert und deserialisiert JSON-Daten automatisch, um komplexe Zustandsobjekte zu speichern.

Im vorangehenden Codebeispiel werden die `currentCount`-Daten als `sessionStorage['count']` im Browser des Benutzers gespeichert. Die Daten werden nicht als Nur-Text gespeichert, sondern mit der Datenschutzlösung von ASP.NET Core geschützt. Die verschlüsselten Daten können untersucht werden, wenn `sessionStorage['count']` in der Entwicklerkonsole des Browsers ausgewertet wird.

Verwenden Sie `ProtectedSessionStore.GetAsync`, um die `currentCount`-Daten wiederherzustellen, wenn der Benutzer zu einem späteren Zeitpunkt zur `Counter`-Komponente zurückkehrt (auch wenn er sich in einer neuen Verbindung befindet):

```csharp
protected override async Task OnInitializedAsync()
{
    var result = await ProtectedSessionStore.GetAsync<int>("count");
    currentCount = result.Success ? result.Value : 0;
}
```

Wenn die Parameter der Komponente den Navigationszustand enthalten, rufen Sie `ProtectedSessionStore.GetAsync` auf, und weisen Sie ein Ergebnis ungleich `null` in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> zu, nicht <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> wird nur ein Mal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> wird später nicht nochmals aufgerufen, wenn der Benutzer zu einer anderen URL navigiert, während er auf der gleichen Seite bleibt. Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle>.

> [!WARNING]
> Die Beispiele in diesem Abschnitt funktionieren nur, wenn für den Server kein Prerendering aktiviert ist. Bei aktiviertem PreRendering wird ein Fehler generiert, der besagt, dass JavaScript-Interop-Aufrufe nicht ausgegeben werden können, weil PreRendering für die Komponente ausgeführt wird.
>
> Deaktivieren Sie entweder Prerendering, oder fügen Sie zusätzlichen Code hinzu, um mit Prerendering zu arbeiten. Weitere Informationen zum Schreiben von Code, der mit Prerendering kompatibel ist, finden Sie im Abschnitt [Handhabung von Prerendering](#handle-prerendering).

### <a name="handle-the-loading-state"></a>Handhabung des Ladezustands

Da die Browserspeicherung asynchron über eine Netzwerkverbindung erfolgt, vergeht immer eine gewisse Zeit, bis die Daten geladen werden und für eine Komponente zur Verfügung stehen. Die besten Ergebnisse erzielen Sie, wenn Sie während des Ladevorgangs eine Ladezustandsmeldung rendern, anstatt leere oder Standarddaten anzuzeigen.

Ein Ansatz besteht darin, nachzuverfolgen, ob die Daten den Zustand `null` aufweisen. Dies bedeutet, dass die Daten noch geladen werden. In der `Counter`-Standardkomponente wird die Anzahl in einer `int` gespeichert. [Legen Sie fest, dass `currentCount` NULL-Werte aufweisen kann](/dotnet/csharp/language-reference/builtin-types/nullable-value-types), indem Sie dem Typ (`int`) ein Fragezeichen (`?`) hinzufügen:

```csharp
private int? currentCount;
```

Anstatt bedingungslos die Anzahl und die Schaltfläche **`Increment`** anzuzeigen, zeigen Sie diese Elemente nur dann an, wenn die Daten geladen wurden, indem Sie <xref:System.Nullable%601.HasValue%2A> überprüfen:

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Handhabung von Prerendering

Während des Prerenderings:

* Eine interaktive Verbindung zum Browser des Benutzers ist nicht vorhanden.
* Der Browser verfügt noch nicht über eine Seite, auf der der JavaScript-Code ausgeführt werden kann.

`localStorage` oder `sessionStorage` sind während des Prerenderings nicht verfügbar. Wenn die Komponente versucht,mit dem Speicher zu interagieren, wird ein Fehler generiert, der besagt, dass JavaScript-Interop-Aufrufe nicht ausgegeben werden können, weil PreRendering für die Komponente ausgeführt wird.

Eine Möglichkeit, den Fehler zu beheben, besteht darin, das Prerendering zu deaktivieren. Dies ist normalerweise die beste Lösung, wenn die App den browserbasierten Speicher stark nutzt. Das Prerendering erhöht die Komplexität und bringt der App keinen Nutzen, da die App keinen nützlichen Inhalt vorab rendern kann, bevor nicht `localStorage` oder `sessionStorage` verfügbar sind.

Öffnen Sie die Datei `Pages/_Host.cshtml`, und ändern Sie das `render-mode`-Attribut des [Komponententag-Hilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>, um PreRendering zu deaktivieren:

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

Das Prerendering ist möglicherweise nützlich für andere Seiten, die `localStorage` oder `sessionStorage` nicht verwenden. Damit PreRendering beibehalten wird, verschieben Sie den Ladevorgang, bis der Browser mit der Verbindung verbunden ist. Im Folgenden finden Sie ein Beispiel für das Speichern eines Zählerwerts:

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int currentCount;
    private bool isConnected;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        var result = await ProtectedLocalStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Ausklammern der Zustandsbeibehaltung an einen allgemeinen Speicherort

Wenn viele Komponenten auf browserbasierte Speicherung zurückgreifen, führt ein erneutes Implementieren des Zustandsanbietercodes häufig zur Duplizierung des Codes. Eine Möglichkeit, die Duplizierung von Code zu vermeiden, besteht darin, eine *übergeordnete Komponente des Zustandsanbieters zu erstellen* , die die Zustandsanbieterlogik kapselt. Untergeordnete Komponenten können ohne Berücksichtigung des Zustandspersistenzmechanismus mit beibehaltenen Daten arbeiten.

Im folgenden Beispiel sehen Sie eine `CounterStateProvider`-Komponente, für die die Zählerdaten in `sessionStorage` persistent gespeichert werden:

```razor
@using Microsoft.AspNetCore.Components.Server.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        var result = await ProtectedSessionStore.GetAsync<int>("count");
        currentCount = result.Success ? result.Value : 0;
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Die `CounterStateProvider`-Komponente verarbeitet die Ladephase, indem der untergeordnete Inhalt erst nach Abschluss des Ladenvorgangs gerendert wird.

Um die `CounterStateProvider`-Komponente zu verwenden, umschließen Sie eine Instanz der Komponente in einer beliebigen anderen Komponente, die Zugriff auf den Zählerzustand benötigt. Um den Zustand für alle Komponenten in einer App zugänglich zu machen, umschließen Sie die `CounterStateProvider`-Komponente um den <xref:Microsoft.AspNetCore.Components.Routing.Router> in der `App`-Komponente (`App.razor`):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Umschließende Komponenten erhalten den beibehaltenen Zählerzustand und können diesen ändern. Die folgende `Counter`-Komponente implementiert das Muster:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Die vorherige Komponente muss weder mit `ProtectedBrowserStorage` interagieren, noch eine „Ladephase“ verarbeiten.

Für eine Handhabung des Prerenderings wie oben beschrieben kann `CounterStateProvider` dahingehend geändert werden, dass alle Komponenten, die Zählerdaten verbrauchen, automatisch mit dem Prerendering arbeiten. Weitere Informationen finden Sie im Abschnitt [Verarbeiten von PreRendering](#handle-prerendering).

In folgenden Fällen wird im Allgemeinen das Muster der *übergeordneten Komponente des Zustandsanbieters* empfohlen:

* Um den Zustand über viele Komponenten hinweg zu nutzen.
* Es muss nur ein Zustandsobjekt auf der obersten Ebene beibehalten werden.

Um viele verschiedene Zustandsobjekte beizubehalten und verschiedene Teilmengen von Objekten an verschiedenen Orten zu verarbeiten, ist es besser, das globale persistente Speichern des Zustands zu vermeiden.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="protected-browser-storage-experimental-nuget-package"></a>Experimentelles NuGet-Paket für Protected Browser Storage

[ASP.NET Core Protected Browser Storage](xref:security/data-protection/introduction) nutzt ASP.NET Core-Datenschutz für [`localStorage`](https://developer.mozilla.org/docs/Web/API/Window/localStorage) und [`sessionStorage`](https://developer.mozilla.org/docs/Web/API/Window/sessionStorage).

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` ist ein nicht unterstütztes experimentelles Paket, das nicht für den Einsatz in der Produktion geeignet ist.
>
> Das Paket ist nur für die Verwendung in ASP.NET Core 3.1 Blazor Server-Apps verfügbar.

### <a name="configuration"></a>Konfiguration

1. Fügen Sie [`Microsoft.AspNetCore.ProtectedBrowserStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) einen Paketverweis hinzu.
1. Fügen Sie in der Datei `Pages/_Host.cshtml` das folgende Skript innerhalb des schließenden Tags `</body>` hinzu:

   ```cshtml
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. Rufen Sie in `Startup.ConfigureServices` `AddProtectedBrowserStorage` auf, um der Dienstsammlung die Dienste `localStorage` und `sessionStorage` hinzuzufügen:

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>Speichern und Laden von Daten in einer Komponente

Verwenden Sie für jede Komponente, die das Laden oder Speichern von Daten im Browserspeicher erfordert, die [`@inject`](xref:mvc/views/razor#inject)-Anweisung, um eine Instanz von einem der folgenden Komponenten einzufügen:

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

Die Auswahl hängt davon ab, welchen Browserspeicherort Sie verwenden möchten. Im folgenden Beispiel wird `sessionStorage` verwendet:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

Die `@using`-Anweisung kann in einer `_Imports.razor`-Datei statt in der Komponente abgelegt werden. Bei Verwendung der `_Imports.razor`-Datei wird der Namespace für größere Segmente der App oder die gesamte App zur Verfügung gestellt.

Um den `currentCount`-Wert in der `Counter`-Komponente einer App basierend auf der Blazor Server-Projektvorlage beizubehalten, ändern Sie die `IncrementCount`-Methode so, dass `ProtectedSessionStore.SetAsync` verwendet wird:

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

Bei größeren, realistischeren Apps ist die Speicherung einzelner Felder ein unwahrscheinliches Szenario. Apps speichern meist ganze Modellobjekte, die komplexe Zustände enthalten. `ProtectedSessionStore` serialisiert und deserialisiert JSON-Daten automatisch.

Im vorangehenden Codebeispiel werden die `currentCount`-Daten als `sessionStorage['count']` im Browser des Benutzers gespeichert. Die Daten werden nicht als Nur-Text gespeichert, sondern mit der Datenschutzlösung von ASP.NET Core geschützt. Die verschlüsselten Daten können untersucht werden, wenn `sessionStorage['count']` in der Entwicklerkonsole des Browsers ausgewertet wird.

Verwenden Sie `ProtectedSessionStore.GetAsync`, um die `currentCount`-Daten wiederherzustellen, wenn der Benutzer zu einem späteren Zeitpunkt zur `Counter`-Komponente zurückkehrt (auch wenn er sich in einer vollständig neuen Verbindung befindet):

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

Wenn die Parameter der Komponente den Navigationszustand enthalten, rufen Sie `ProtectedSessionStore.GetAsync` auf, und weisen Sie das Ergebnis in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> und nicht in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> zu. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> wird nur ein Mal aufgerufen, wenn die Komponente zum ersten Mal instanziiert wird. <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> wird später nicht nochmals aufgerufen, wenn der Benutzer zu einer anderen URL navigiert, während er auf der gleichen Seite bleibt. Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle>.

> [!WARNING]
> Die Beispiele in diesem Abschnitt funktionieren nur, wenn für den Server kein Prerendering aktiviert ist. Bei aktiviertem PreRendering wird ein Fehler generiert, der besagt, dass JavaScript-Interop-Aufrufe nicht ausgegeben werden können, weil PreRendering für die Komponente ausgeführt wird.
>
> Deaktivieren Sie entweder Prerendering, oder fügen Sie zusätzlichen Code hinzu, um mit Prerendering zu arbeiten. Weitere Informationen zum Schreiben von Code, der mit Prerendering kompatibel ist, finden Sie im Abschnitt [Handhabung von Prerendering](#handle-prerendering).

### <a name="handle-the-loading-state"></a>Handhabung des Ladezustands

Da die Browserspeicherung asynchron über eine Netzwerkverbindung erfolgt, vergeht immer eine gewisse Zeit, bis die Daten geladen werden und für eine Komponente zur Verfügung stehen. Die besten Ergebnisse erzielen Sie, wenn Sie während des Ladevorgangs eine Ladezustandsmeldung rendern, anstatt leere oder Standarddaten anzuzeigen.

Ein Ansatz besteht darin, nachzuverfolgen, ob die Daten den Zustand `null` aufweisen. Dies bedeutet, dass die Daten noch geladen werden. In der `Counter`-Standardkomponente wird die Anzahl in einer `int` gespeichert. [Legen Sie fest, dass `currentCount` NULL-Werte aufweisen kann](/dotnet/csharp/language-reference/builtin-types/nullable-value-types), indem Sie dem Typ (`int`) ein Fragezeichen (`?`) hinzufügen:

```csharp
private int? currentCount;
```

Anstatt bedingungslos die Anzahl und die Schaltfläche **`Increment`** anzuzeigen, legen Sie fest, dass diese Elemente nur dann angezeigt werden, wenn die Daten geladen sind:

```razor
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>Handhabung von Prerendering

Während des Prerenderings:

* Eine interaktive Verbindung zum Browser des Benutzers ist nicht vorhanden.
* Der Browser verfügt noch nicht über eine Seite, auf der der JavaScript-Code ausgeführt werden kann.

`localStorage` oder `sessionStorage` sind während des Prerenderings nicht verfügbar. Wenn die Komponente versucht,mit dem Speicher zu interagieren, wird ein Fehler generiert, der besagt, dass JavaScript-Interop-Aufrufe nicht ausgegeben werden können, weil PreRendering für die Komponente ausgeführt wird.

Eine Möglichkeit, den Fehler zu beheben, besteht darin, das Prerendering zu deaktivieren. Dies ist normalerweise die beste Lösung, wenn die App den browserbasierten Speicher stark nutzt. Das Prerendering erhöht die Komplexität und bringt der App keinen Nutzen, da die App keinen nützlichen Inhalt vorab rendern kann, bevor nicht `localStorage` oder `sessionStorage` verfügbar sind.

Öffnen Sie die Datei `Pages/_Host.cshtml`, und ändern Sie das `render-mode`-Attribut des [Komponententag-Hilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>, um PreRendering zu deaktivieren:

```cshtml
<component type="typeof(App)" render-mode="Server" />
```

Das Prerendering ist möglicherweise nützlich für andere Seiten, die `localStorage` oder `sessionStorage` nicht verwenden. Damit PreRendering beibehalten wird, verschieben Sie den Ladevorgang, bis der Browser mit der Verbindung verbunden ist. Im Folgenden finden Sie ein Beispiel für das Speichern eines Zählerwerts:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

@if (isConnected)
{
    <p>Current count: <strong>@currentCount</strong></p>
    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}

@code {
    private int? currentCount;
    private bool isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("count");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedLocalStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>Ausklammern der Zustandsbeibehaltung an einen allgemeinen Speicherort

Wenn viele Komponenten auf browserbasierte Speicherung zurückgreifen, führt ein erneutes Implementieren des Zustandsanbietercodes häufig zur Duplizierung des Codes. Eine Möglichkeit, die Duplizierung von Code zu vermeiden, besteht darin, eine *übergeordnete Komponente des Zustandsanbieters zu erstellen* , die die Zustandsanbieterlogik kapselt. Untergeordnete Komponenten können ohne Berücksichtigung des Zustandspersistenzmechanismus mit beibehaltenen Daten arbeiten.

Im folgenden Beispiel sehen Sie eine `CounterStateProvider`-Komponente, für die die Zählerdaten in `sessionStorage` persistent gespeichert werden:

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (isLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool isLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        isLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

Die `CounterStateProvider`-Komponente verarbeitet die Ladephase, indem der untergeordnete Inhalt erst nach Abschluss des Ladenvorgangs gerendert wird.

Um die `CounterStateProvider`-Komponente zu verwenden, umschließen Sie eine Instanz der Komponente in einer beliebigen anderen Komponente, die Zugriff auf den Zählerzustand benötigt. Um den Zustand für alle Komponenten in einer App zugänglich zu machen, umschließen Sie die `CounterStateProvider`-Komponente um den <xref:Microsoft.AspNetCore.Components.Routing.Router> in der `App`-Komponente (`App.razor`):

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

Umschließende Komponenten erhalten den beibehaltenen Zählerzustand und können diesen ändern. Die folgende `Counter`-Komponente implementiert das Muster:

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>
<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

Die vorherige Komponente muss weder mit `ProtectedBrowserStorage` interagieren, noch eine „Ladephase“ verarbeiten.

Für eine Handhabung des Prerenderings wie oben beschrieben kann `CounterStateProvider` dahingehend geändert werden, dass alle Komponenten, die Zählerdaten verbrauchen, automatisch mit dem Prerendering arbeiten. Weitere Informationen finden Sie im Abschnitt [Verarbeiten von PreRendering](#handle-prerendering).

In folgenden Fällen wird im Allgemeinen das Muster der *übergeordneten Komponente des Zustandsanbieters* empfohlen:

* Um den Zustand über viele Komponenten hinweg zu nutzen.
* Es muss nur ein Zustandsobjekt auf der obersten Ebene beibehalten werden.

Um viele verschiedene Zustandsobjekte beizubehalten und verschiedene Teilmengen von Objekten an verschiedenen Orten zu verarbeiten, ist es besser, das globale persistente Speichern des Zustands zu vermeiden.

::: moniker-end

## <a name="in-memory-state-container-service"></a>Arbeitsspeicherinterner Zustandscontainer des Diensts

[!INCLUDE[](~/includes/blazor-state-management/state-container.md)]

::: zone-end
