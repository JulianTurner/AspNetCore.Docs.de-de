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
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>Fehlerbehandlung in ASP.NET Core Blazor-Apps

Von [Steve Sanderson](https://github.com/SteveSandersonMS)

In diesem Artikel wird beschrieben, wie Blazor Ausnahmefehler behandelt und wie Sie Apps entwickeln können, die Fehler erkennen und behandeln.

## <a name="detailed-errors-during-development"></a>Ausführliche Fehler bei der Entwicklung

Wenn eine Blazor-App während der Entwicklung nicht ordnungsgemäß funktioniert, erhalten Sie nun ausführliche Fehlerinformationen von der App, die Sie beim Beheben des Problems unterstützen. Wenn ein Fehler auftritt, zeigen Blazor-Apps eine goldene Leiste am unteren Rand der Anzeige an:

* Während der Entwicklung leitet die goldene Leiste Sie an die Browserkonsole weiter, in der die Ausnahme angezeigt wird.
* In der Produktion benachrichtigt die goldene Leiste den Benutzer darüber, dass ein Fehler aufgetreten ist, und empfiehlt eine Aktualisierung des Browsers.

Die Benutzeroberfläche für diese Fehlerbehandlung ist Teil der Blazor-Projektvorlagen.

Passen Sie in einer Blazor WebAssembly-App die Darstellung in der `wwwroot/index.html`-Datei an:

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

Passen Sie in einer Blazor Server-App die Darstellung in der `Pages/_Host.cshtml`-Datei an:

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

Das `blazor-error-ui`-Element wird von den Stilen in den Blazor-Vorlagen (`wwwroot/css/app.css` oder `wwwroot/css/site.css`) verborgen und nur angezeigt, wenn ein Fehler auftritt:

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

## <a name="blazor-server-detailed-circuit-errors"></a>Detaillierte Blazor Server-Leitungsfehler

Clientseitige Fehlermeldungen enthalten weder den Aufrufstapel noch Details zur Fehlerursache, Serverprotokolle hingegen schon. Zu Entwicklungszwecken können vertrauliche Informationen zu Leitungsfehlern für den Client verfügbar gemacht werden, indem Sie detaillierte Fehlermeldungen aktivieren.

Aktivieren Sie detaillierte Blazor Server-Fehler mithilfe der folgenden Verfahren:

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.
* Legen Sie den Konfigurationsschlüssel `DetailedErrors` auf `true` fest. Diese Festlegung kann in der Datei mit den Entwicklungseinstellungen der App (`appsettings.Development.json`) erfolgen. Der Schlüssel lässt sich darüber hinaus mithilfe der Umgebungsvariablen `ASPNETCORE_DETAILEDERRORS` auf den Wert `true` festlegen.
* Die [serverseitige Protokollierung von SignalR](xref:signalr/diagnostics#server-side-logging) (`Microsoft.AspNetCore.SignalR`) kann auf [Debuggen](xref:Microsoft.Extensions.Logging.LogLevel) oder [Ablaufverfolgung](xref:Microsoft.Extensions.Logging.LogLevel) zur detaillierten Protokollierung von SignalR festgelegt werden.

`appsettings.Development.json`:

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
> Das Verfügbarmachen von Fehlerinformationen für internetseitige Clients ist ein Sicherheitsrisiko, dass immer vermieden werden sollte.

## <a name="how-a-blazor-server-app-reacts-to-unhandled-exceptions"></a>Reaktion einer Blazor Server-App auf Ausnahmefehler

Blazor Server ist ein zustandsbehaftetes Framework. Während Benutzer mit einer App interagieren, besteht eine Verbindung mit dem Server, die als *Leitung* bezeichnet wird. Die Leitung enthält aktive Instanzen von Komponenten und weist zahlreiche andere Aspekte zum Zustand auf wie:

* Die zuletzt gerenderte Ausgabe von Komponenten
* Die aktuellen Delegate zur Ereignisbehandlung, die durch clientseitige Ereignisse ausgelöst werden können

Wenn ein Benutzer die App in mehreren Registerkarten eines Browsers öffnet, bestehen mehrere unabhängige Leitungen.

Die meisten Ausnahmefehler werden von Blazor als für die Leitung schwerwiegende Fehler behandelt. Wenn eine Leitung aufgrund eines Ausnahmefehlers beendet wird, kann der Benutzer nur dann mit der App weiter interagieren, wenn die Seite nochmals geladen und so eine neue Leitung erstellt wird. Leitungen außerhalb der beendeten Leitung, bei denen es sich um Leitungen für andere Benutzer oder andere Browserregisterkarten handelt, sind davon nicht betroffen. Dieses Szenario ähnelt einer Desktop-App, die abstürzt. Die abgestürzte App muss neu gestartet werden, aber andere Apps sind nicht betroffen.

Eine Leitung wird beendet, wenn aus folgenden Gründen ein Ausnahmefehler auftritt:

* Bei einem Ausnahmefehler wird die Leitung häufig in einen nicht definierten Zustand versetzt.
* Nach einem Ausnahmefehler kann ein normales Funktionieren der App nicht mehr sichergestellt werden.
* Wenn die Leitung weiter bestehen bleibt, können für die App Sicherheitsrisiken bestehen.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Behandeln von Ausnahmefehlern in Entwicklercode

Damit die Ausführung einer App nach einem Fehler fortgesetzt werden kann, muss die App eine Fehlerbehandlungslogik enthalten. In späteren Abschnitten dieses Artikels werden mögliche Quellen für Ausnahmefehler beschrieben.

Rendern Sie in einer Produktionsumgebung keine Ausnahmemeldungen des Frameworks oder Stapelüberwachungen in der Benutzeroberfläche. Durch Rendern von Ausnahmemeldungen oder Stapelüberwachungen kann Folgendes geschehen:

* Vertrauliche Informationen werden gegenüber Endbenutzern offengelegt.
* Böswilligen Benutzern wird geholfen, Schwachstellen in einer App zu erkennen, die die Sicherheit von Server, App oder Netzwerk beeinträchtigen können.

## <a name="log-errors-with-a-persistent-provider"></a>Protokollieren von Fehlern bei einem permanenten Anbieter

Im Fall eines Ausnahmefehlers wird die Ausnahme in den im Dienstcontainer konfigurierten<xref:Microsoft.Extensions.Logging.ILogger>-Instanzen protokolliert. Blazor-Apps protokollieren standardmäßig in die Konsolenausgabe beim Konsolenprotokollierungsanbieter. Überlegen Sie, ob Sie für die Protokollierung besser einen permanenteren Speicherort bei einem Anbieter verwenden, der Protokollgröße und Protokollrotation verwaltet. Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.

Während der Entwicklung sendet Blazor zur Unterstützung beim Debuggen in der Regel alle Ausnahmedetails an die Konsole des Browsers. In einer Produktionsumgebung sind ausführliche Fehler in der Konsole des Browsers standardmäßig deaktiviert. Das bedeutet, dass Fehler nicht an Clients gesendet werden. Es werden jedoch alle Details der Ausnahme serverseitig protokolliert. Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.

Sie müssen entscheiden, welche Vorfälle protokolliert werden sollen. Zudem müssen Sie den Schweregrad für protokollierte Vorfälle festlegen. Feindliche Benutzer können Fehler möglicherweise absichtlich auslöst. Protokollieren Sie beispielsweise keinen Vorfall aus einem Fehler, bei dem eine unbekannte `ProductId` in der URL einer Komponente angegeben wurde, über die Produktdetails angezeigt werden. Nicht alle Fehler sollten als zu protokollierende Vorfälle mit hohem Schweregrad behandelt werden.

Weitere Informationen finden Sie unter <xref:blazor/fundamentals/logging>.

## <a name="places-where-errors-may-occur"></a>Stellen, an denen Fehler auftreten können

Framework- und App-Code kann an den folgenden Stellen Ausnahmefehler auslösen:

* [Komponenteninstanziierung](#component-instantiation)
* [Lebenszyklusmethoden](#lifecycle-methods)
* [Renderinglogik](#rendering-logic)
* [Ereignishandler](#event-handlers)
* [Beseitigung von Komponenten](#component-disposal)
* [JavaScript-Interoperabilität](#javascript-interop)
* [Blazor Server-Rerendering](#blazor-server-prerendering)

Die genannten Ausnahmefehler werden in den folgenden Abschnitten dieses Artikels beschrieben.

### <a name="component-instantiation"></a>Komponenteninstanziierung

Wenn durch Blazor eine Instanz einer Komponente erstellt wird:

* Wird der Konstruktor der Komponente aufgerufen.
* Werden die Konstruktoren von Nicht-Singleton-DI-Diensten aufgerufen, die dem Konstruktor der Komponente mit der Anweisung [`@inject`](xref:mvc/views/razor#inject) oder mit dem Attribut [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) bereitgestellt werden.

Bei einer Blazor Server-Leitung tritt ein Fehler auf, wenn ein ausgeführter Konstruktor oder ein Setter für eine `[Inject]`-Eigenschaft einen Ausnahmefehler auslöst. Die Ausnahme ist schwerwiegend, weil die Komponente durch das Framework nicht instanziiert werden kann. Wenn eine Konstruktorlogik Ausnahmen auslösen kann, muss die App die Ausnahmen mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung abfangen.

### <a name="lifecycle-methods"></a>Lebenszyklusmethoden

Während der Lebensdauer einer Komponente ruft Blazor die folgenden [Lebenszyklusmethoden](xref:blazor/components/lifecycle) aufrufen:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

Wenn eine Lebenszyklusmethode synchron oder asynchron eine Ausnahme auslöst, ist die Ausnahme für eine Blazor Server-Leitung schwerwiegend. Damit Komponenten Fehler in Lebenszyklusmethoden behandeln können, fügen Sie eine Fehlerbehandlungslogik hinzu.

Im folgenden Beispiel, in dem <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> eine Methode zum Aufrufen eines Produkts aufruft:

* Wird eine Ausnahme, die in der `ProductRepository.GetProductByIdAsync`-Methode ausgelöst wird, durch eine [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung behandelt.
* Wenn der `catch`-Block ausgeführt wird:
  * wird `loadFailed` auf `true` festgelegt, sodass dem Benutzer eine Fehlermeldung angezeigt wird.
  * wird der Fehler protokolliert.

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Renderinglogik

Das deklarative Markup in einer `.razor`-Komponentendatei wird in eine C#-Methode namens <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> kompiliert. Beim Rendern einer Komponente wird von <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> eine Datenstruktur ausgeführt und erstellt, die Elemente, Text und untergeordnete Komponenten der gerenderten Komponente beschreibt.

Die Renderinglogik kann eine Ausnahme auslösen. Ein Beispiel für dieses Szenario tritt auf, wenn `@someObject.PropertyName` ausgewertet wird, `@someObject` jedoch `null` ist. Ein von einer Renderinglogik ausgelöster Ausnahmefehler ist für eine Blazor Server-Leitung schwerwiegend.

Um in einer Renderinglogik eine Nullverweisausnahme zu vermeiden, prüfen Sie vor dem Zugriff auf die entsprechenden Member, ob ein `null`-Objekt vorhanden ist. Im folgenden Beispiel wird auf die `person.Address`-Eigenschaften nicht zugegriffen, wenn `person.Address` `null` ist:

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Für den obigen Code wird angenommen, dass `person` nicht `null` ist. Häufig wird durch die Codestruktur sichergestellt, dass ein Objekt zu dem Zeitpunkt vorhanden ist, zu dem die Komponente gerendert wird. In diesen Fällen muss nicht geprüft werden, ob `null` in der Renderinglogik vorhanden ist. Im obigen Beispiel kann garantiert werden, dass `person` vorhanden ist, da `person` beim Instanziieren der Komponente erstellt wird.

### <a name="event-handlers"></a>Ereignishandler

Clientseitiger Code löst Aufrufe von C#-Code aus, wenn Ereignishandler mit folgenden Elementen erstellt werden:

* `@onclick`
* `@onchange`
* Mit anderen `@on...`-Attributen
* `@bind`

Der Ereignishandlercode löst in diesen Szenarios möglicherweise einen Ausnahmefehler aus.

Wenn ein Ereignishandler einen Ausnahmefehler (z. B. einen Datenbankabfragefehler) auslöst, ist die Ausnahme für eine Blazor Server-Leitung schwerwiegend. Für den Fall, dass durch die App Code aufgerufen wird, der aus externen Gründen einen Fehler verursachen könnte, müssen Ausnahmen mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung abgefangen werden.

Wenn die Ausnahme nicht durch Benutzercode abgefangen und behandelt wird, wird sie durch das Framework protokolliert, und die Leitung wird beendet.

### <a name="component-disposal"></a>Beseitigung von Komponenten

Eine Komponente kann von der Benutzeroberfläche entfernt werden, weil der Benutzer beispielsweise zu einer anderen Seite navigiert ist. Wenn eine Komponente, die <xref:System.IDisposable?displayProperty=fullName> implementiert, von der Benutzeroberfläche entfernt wird, ruft das Framework die <xref:System.IDisposable.Dispose%2A>-Methode der Komponente auf.

Wenn die `Dispose`-Methode der Komponente einen Ausnahmefehler auslöst, ist die Ausnahme für eine Blazor Server-Leitung schwerwiegend. Wenn eine Beseitigungslogik Ausnahmen auslösen kann, muss die App die Ausnahmen mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung abfangen.

Weitere Informationen zur Beseitigung von Komponenten finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

### <a name="javascript-interop"></a>JavaScript-Interoperabilität

Mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> kann .NET-Code die JavaScript-Laufzeit im Browser des Benutzers asynchron aufrufen.

Die folgenden Bedingungen gelten für die Fehlerbehandlung mit <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A>:

* Wenn ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> synchron fehlschlägt, tritt eine .NET-Ausnahme auf. Ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> kann beispielsweise fehlschlagen, wenn die bereitgestellten Argumente nicht serialisiert werden können. Die Ausnahme muss vom Entwicklercode abgefangen werden. Wenn eine Ausnahme nicht von App-Code in einem Ereignishandler oder in der Lebenszyklusmethode einer Komponente behandelt wird, ist die resultierende Ausnahme für eine Blazor Server-Leitung schwerwiegend.
* Wenn ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> asynchron fehlschlägt, schlägt die .NET <xref:System.Threading.Tasks.Task> fehl. Ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> kann beispielsweise fehlschlagen, wenn der JavaScript-Code eine Ausnahme auslöst oder eine `Promise` zurückgibt, die als `rejected` abgeschlossen wird. Die Ausnahme muss vom Entwicklercode abgefangen werden. Wenn Sie den [`await`](/dotnet/csharp/language-reference/keywords/await)-Operator verwenden, sollten Sie in Erwägung ziehen, den Methodenaufruf mithilfe einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit Fehlerbehandlung und Fehlerprotokollierung zu umschließen. Tun Sie dies nicht, führt der fehlerhafte Code zu einem Ausnahmefehler, der für eine Blazor Server-Leitung schwerwiegend ist.
* Ein Aufruf von <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> muss standardmäßig innerhalb eines bestimmten Zeitraums abgeschlossen werden, da ansonsten für den Aufruf ein Timeout auftritt. Die Standardwert für das Zeitlimit beträgt eine Minute. Mit dem Zeitlimit wird der Code vor dem Verlust der Netzwerkkonnektivität oder vor JavaScript-Code geschützt, der keine Abschlussmeldung sendet. Wenn beim Aufruf ein Timeout auftritt, schlägt die resultierende <xref:System.Threading.Tasks> mit einer <xref:System.OperationCanceledException>fehl. Die Ausnahme wird abgefangen und mit Protokollierung verarbeitet.

Ähnlich kann JavaScript-Code Aufrufe von .NET-Methoden initiieren, die durch das [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript)-Attribut angegeben werden. Wenn diese .NET-Methoden einen Ausnahmefehler auslösen:

* wird die Ausnahme nicht als schwerwiegend für eine Blazor Server-Leitung behandelt.
* wird die JavaScript-seitige `Promise` abgelehnt.

Sie können Fehlerbehandlungscode auf der .NET-Seite oder auf der JavaScript-Seite des Methodenaufrufs verwenden.

Weitere Informationen finden Sie in den folgenden Artikeln:

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="blazor-server-prerendering"></a>Blazor Server-Prerendering

Blazor-Komponenten können mit dem [Komponententaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) vorab gerendert werden, sodass das gerenderte HTML-Markup als Teil der ursprünglichen HTTP-Anforderung des Benutzers zurückgegeben wird. Dies funktioniert durch:

* Erstellen einer neuen Leitung für alle vorab gerenderten Komponenten, die Teil derselben Seite sind
* Generieren des ursprünglichen HTML-Codes
* Behandeln der Leitung als `disconnected`, bis der Browser des Benutzers eine SignalR-Verbindung zurück zum selben Server einrichtet. Wenn die Verbindung hergestellt wird, wird die Interaktivität in der Leitung fortgesetzt und das HTML-Markup der Komponenten wird aktualisiert.

Wenn von einer Komponente während des Prerenderings ein Ausnahmefehler beispielswiese während einer Lebenszyklusmethode oder in der Renderinglogik ausgelöst wird, gilt Folgendes:

* Die Ausnahme ist für die Leitung schwerwiegend.
* Die Ausnahme wird vom <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>-Taghilfsprogramm in der Aufrufliste weiter oben ausgelöst. Daher schlägt die gesamte HTTP-Anforderung fehl, es sei denn, die Ausnahme wird explizit vom Entwicklercode abgefangen.

Wenn das Prerendering unter normalen Umständen fehlschlägt, ist es nicht sinnvoll, mit dem Erstellen und Rendern der Komponente fortzufahren, da eine funktionierende Komponente nicht gerendert werden kann.

Wenn während des Prerenderings auftretende Fehler toleriert werden sollen, muss sich die Fehlerbehandlungslogik in einer Komponente befinden, die möglicherweise Ausnahmen auslöst. Verwenden Sie [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisungen mit Fehlerbehandlung und Fehlerprotokollierung. Statt das <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>-Tag-Hilfsprogramm in einer [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung zu umschließen, fügen Sie eine Fehlerbehandlungslogik in die vom <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>-Tag-Hilfsprogramm gerenderte Komponente ein.

## <a name="advanced-scenarios"></a>Erweiterte Szenarien

### <a name="recursive-rendering"></a>Rekursives Rendering

Komponenten können rekursiv geschachtelt sein. Dies ist nützlich, um rekursive Datenstrukturen darzustellen. So kann eine `TreeNode`-Komponente beispielsweise weitere `TreeNode`-Komponenten für die einzelnen untergeordneten Elemente des Knotens rendern.

Vermeiden Sie beim rekursiven Rendern Codemuster, die zu einer Endlosschleife führen:

* Vermeiden Sie rekursives Rendering bei einer Datenstruktur, die einen Zyklus enthält. Rendern Sie beispielsweise keinen Strukturknoten, dessen untergeordnete Knoten sich selbst enthalten.
* Erstellen Sie keine Layoutkette, die einen Zyklus enthält. Erstellen Sie beispielsweise kein Layout, das sein eigenes Layout ist.
* Lassen Sie nicht zu, dass ein Endbenutzer Rekursionsinvarianten (Regeln) durch eine böswillige Dateneingabe oder JavaScript-Interoperabilitätsaufrufe verletzt.

Endlosschleifen durch Rendering:

* sorgt dafür, dass der Renderingprozess unendlich fortgesetzt wird.
* entspricht dem Erstellen einer nicht abgeschlossenen Schleife.

In diesen Szenarios tritt ein Fehler bei einer betroffenen Blazor Server-Leitung auf, und der Thread versucht in der Regel Folgendes:

* Unbegrenzt so viel CPU-Zeit wie vom Betriebssystem zulässig zu nutzen.
* Eine unbegrenzte Menge an Serverarbeitsspeicher zu nutzen. Das Belegen einer unbegrenzte Menge an Arbeitsspeicher entspricht einem Szenario, bei dem eine nicht beendete Schleife einer Auflistung bei jeder Iteration Einträge hinzufügt.

Wenn Sie Muster mit Endlosrekursionen vermeiden möchten, müssen Sie sicherstellen, dass der Renderingcode geeignete Beendigungsbedingungen enthält.

### <a name="custom-render-tree-logic"></a>Benutzerdefinierte Renderstrukturlogik

Die meisten Blazor-Komponenten werden als `.razor`-Dateien implementiert und kompiliert, um eine Logik zu erzeugen, die einen <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> verwendet, um dessen Ausgabe zu rendern. Ein Entwickler kann mit prozeduralem C#-Code eine <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Logik manuell implementieren. Weitere Informationen finden Sie unter <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>.

> [!WARNING]
> Die Verwendung einer manuellen Buildlogik für die Renderstruktur gilt als erweitertes und unsicheres Szenario, das für die allgemeine Komponentenentwicklung nicht empfohlen wird.

Beim Schreiben von <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder>-Code muss der Entwickler die Richtigkeit des Codes garantieren. Der Entwickler muss beispielsweise Folgendes sicherstellen:

* Aufrufe von <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> und <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> sind ordnungsgemäß ausgeglichen.
* Attribute werden nur an den richtigen Stellen hinzugefügt.

Eine fehlerhafte Buildlogik für die Renderstruktur kann ein willkürliches undefiniertes Verhalten wie Abstürze, Serverabstürze und Sicherheitsrisiken verursachen.

Eine manuelle Buildlogik für die Renderstruktur ist genauso komplex und birgt dasselbe Maß an *Gefahren* wie das manuelle Schreiben von Assemblycode oder MSIL-Anweisungen.
