---
title: Best Practices zur Blazor WebAssembly-Leistung in ASP.NET Core
author: pranavkm
description: In diesem Artikel erhalten Sie Tipps für das Steigern der Leistung von Blazor WebAssembly-Apps in ASP.NET Core und das Vermeiden von häufig auftretenden Leistungsproblemen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 5d3cd1480dd37f437b2d6d5a89af0a842286be95
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080263"
---
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a>Best Practices zur Blazor WebAssembly-Leistung in ASP.NET Core

Von [Pranav Krishnamoorthy](https://github.com/pranavkm)

In diesem Artikel finden Sie Richtlinien zu Best Practices für die Leistung von Blazor WebAssembly-Apps in ASP.NET Core.

## <a name="avoid-unnecessary-component-renders"></a>Vermeiden von unnötigem Komponentenrendering

Der Vergleichsalgorithmus von Blazor verhindert das Rendern einer Komponente, wenn der Algorithmus erkennt, dass die Komponente nicht geändert wurde. Überschreiben Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType>, wenn Sie eine genaue Kontrolle über das Rendern von Komponenten haben möchten.

Wenn eine nur auf die Benutzeroberfläche bezogene Komponente erstellt wird, die sich nach dem ursprünglichen Rendern nicht mehr ändert, konfigurieren Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> so, dass `false` zurückgegeben wird:

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

Für die meisten Apps ist eine genaue Kontrolle nicht erforderlich, aber <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> kann verwendet werden, um eine Komponente, die auf ein Benutzeroberflächenereignis antwortet, selektiv zu rendern. Die Verwendung von <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> könnte auch für Szenarien wichtig sein, in denen eine große Anzahl von Komponenten gerendert wird. Stellen Sie sich ein Raster vor, bei dem die Verwendung von <xref:Microsoft.AspNetCore.Components.EventCallback> in einer Komponente in einer Zelle des Rasters <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> im Raster aufruft. Das Aufrufen von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bewirkt, dass alle untergeordneten Komponenten erneut gerendert werden. Wenn nur eine kleine Anzahl von Zellen erneut gerendert werden muss, vermeiden Sie mit <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Leistungseinbußen durch unnötige Renderings.

Im folgenden Beispiel:

* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wird überschrieben und auf den Wert des <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>-Felds festgelegt, der ursprünglich `false` ist, wenn die Komponente geladen wird.
* Wenn auf die Schaltfläche geklickt wird, wird <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> auf `true` festgelegt, was erzwingt, dass die Komponente noch mal mit aktualisierter `currentCount`-Eigenschaft gerendert wird.
* Unmittelbar nach dem erneuten Rendern legt <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> den Wert von <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wieder auf `false` fest, um zukünftiges erneutes Rendern bis zum nächsten Klick auf die Schaltfläche zu verhindern.

```razor
<p>Current count: @currentCount</p>

<button @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;
    private bool shouldRender;

    protected override bool ShouldRender() => shouldRender;

    protected override void OnAfterRender(bool first)
    {
        shouldRender = false;
    }

    private void IncrementCount()
    {
        currentCount++;
        shouldRender = true;
    }
}
```

Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle#after-component-render>.

## <a name="virtualize-re-usable-fragments"></a>Virtualisieren wiederverwendbarer Fragmente

Komponenten stellen einen bequemen Ansatz dar, um wiederverwendbare Code- und Markupfragmente zu generieren. Allgemein wird empfohlen, einzelne Komponenten zu erstellen, die am besten die Anforderungen der App erfüllen können. Eine Einschränkung besteht darin, dass die einzelnen zusätzlichen untergeordneten Komponenten zur Gesamtzeit beitragen, die benötigt wird, eine übergeordnete Komponente zu rendern. Bei den meisten Apps kann dieser zusätzliche Aufwand vernachlässigt werden. Für Apps, die eine große Anzahl Komponenten erzeugen, sollte die Verwendung von Strategien in Betracht gezogen werden, die den Verarbeitungsaufwand reduzieren, z. B. indem die Anzahl gerenderter Komponenten begrenzt wird.

Bei einem Raster oder einer Liste, die Hunderte Datensätze rendert, die Komponenten enthalten, ist die Auslastung des Prozessors für das Rendern beispielsweise höher. Ziehen Sie es in Betracht, ein Raster oder ein Listenlayout zu virtualisieren, sodass nur eine Teilmenge der Komponenten jeweils gleichzeitig gerendert wird. Ein Beispiel für das Rendern einer Teilmenge von Komponenten finden Sie bei den folgenden Komponenten unter [`Virtualization`-Beispiel-App (aspnet/samples, GitHub-Repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):

* `Virtualize`-Komponente ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): Eine in C# geschriebene Komponente, die <xref:Microsoft.AspNetCore.Components.ComponentBase> implementiert, um mehrere Wetterdatensätze basierend auf dem Scrollen des Benutzers zu rendern
* `FetchData`-Komponente ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Verwendet die `Virtualize`-Komponente, um 25 Wetterdatensätze gleichzeitig anzuzeigen

## <a name="avoid-javascript-interop-to-marshal-data"></a>Vermeiden von JavaScript-Interoperabilität zum Marshallen von Daten

In Blazor WebAssembly muss ein JavaScript-Interop-Aufruf (JS) die WebAssembly-JS-Grenze überschreiten. Das Serialisieren und Deserialisieren von Inhalt für die beiden Kontexte führt zu Verarbeitungsaufwand für die App. Häufige JS-Interop-Aufrufe beeinträchtigen die Leistung oftmals. Bestimmen Sie, ob die App viele kleine Nutzlasten in eine einzelne große Payload konsolidieren kann, um das Marshallen der Daten über die Grenze hinweg zu reduzieren, indem das hohe Volumen des Kontextwechselns zwischen WebAssembly und JS vermieden wird.

## <a name="use-systemtextjson"></a>Verwenden von System.Text.Json

Die JS-Interop-Implementierung von Blazor basiert auf <xref:System.Text.Json>. Dabei handelt es sich um eine JSON-Hochleistungsserialisierungsbibliothek mit niedriger Arbeitsspeicherzuteilung. Das Verwenden von <xref:System.Text.Json> führt nicht zu einer zusätzlichen Nutzlastgröße für die App, wenn eine oder mehrere alternative JSON-Bibliotheken hinzufügt werden.

Eine Anleitung zur Migration finden Sie unter [Migration von `Newtonsoft.Json` zu `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a>Verwenden von synchronen und nicht gemarshallten JS-Interop-APIs (wenn möglich)

Blazor WebAssembly bietet zwei zusätzliche Versionen von <xref:Microsoft.JSInterop.IJSRuntime> zusätzlich zur einzelnen für Blazor Server-Apps verfügbaren Version:

* <xref:Microsoft.JSInterop.IJSInProcessRuntime> ermöglicht das synchrone Aufrufen von JS-Interop-Aufrufen, was im Vergleich zu asynchronen Versionen zu weniger Aufwand führt:

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (IJSInProcessRuntime)JS;

          var value = jsInProcess.Invoke<string>("jsInteropCall");
      }
  }
  ```

* <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> ermöglicht nicht gemarshallte JS-Interop-Aufrufe:

  ```javascript
  function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
  }
  ```

  ```razor
  @inject IJSRuntime JS

  @code {
      protected override void OnInitialized()
      {
          var jsInProcess = (WebAssemblyJSRuntime)JS;

          var value = jsInProcess.InvokeUnmarshalled<string>("jsInteropCall");
      }
  }
  ```

  > [!WARNING]
  > Während die Verwendung von <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> den geringsten Aufwand der JS-Interop-Ansätze hat, gibt es für die JavaScript-APIs, die erforderlich sind, um mit diesen APIs zu interagieren, aktuell keine Dokumentation. Außerdem werden in diesem Bereich im Rahmen zukünftiger Releases Breaking Changes vorgenommen.

## <a name="reduce-app-size"></a>Reduzieren der App-Größe

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a>IL-Kürzung (Intermediate Language, Zwischensprache)

[Das Kürzen nicht verwendeter Assemblys über eine Blazor WebAssembly-App](xref:blazor/host-and-deploy/configure-trimmer) reduziert die Größe der App, indem nicht genutzter Code in den Binärdateien der App entfernt wird. Der Trimmer wird standardmäßig beim Veröffentlichen einer Anwendung ausgeführt. Veröffentlichen Sie die App für die Bereitstellung mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) mit auf `Release` festgelegter Option [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), um von der Kürzung zu profitieren:

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a>Intermediate Language-Verknüpfung (IL)

[Das Konfigurieren eines Linkers für eine Blazor WebAssembly-App](xref:blazor/host-and-deploy/configure-linker) reduziert die Größe der App, indem nicht genutzter Code in den Binärdateien der App gekürzt wird. Standardmäßig ist der IL-Linker (Intermediate Language, Zwischensprache) nur aktiviert, wenn der Erstellvorgang in der `Release`-Konfiguration ausgeführt wird. Veröffentlichen Sie die App für die Bereitstellung mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) mit auf `Release` festgelegter Option [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), um diesen Vorteil zu nutzen:

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a>Lazy Loading-Assemblys

Laden Sie Assemblys zur Laufzeit, wenn die Assemblys von einer Route benötigt werden. Weitere Informationen finden Sie unter <xref:blazor/webassembly-lazy-load-assemblies>.

### <a name="compression"></a>Komprimierung

Wenn eine Blazor WebAssembly-App veröffentlicht wird, wird die Ausgabe bei der Veröffentlichung statisch komprimiert, um die App-Größe zu verringern und den Aufwand für eine Laufzeitkomprimierung zu beseitigen. Blazor basiert darauf, dass der Server eine Inhaltsaushandlung ausführt und statisch komprimierte Dateien bereitstellt.

Nach der Bereitstellung einer App überprüfen Sie, ob die App komprimierte Dateien bereitstellt. Sehen Sie sich die Netzwerkregisterkarte der Entwicklertools eines Browsers an, und überprüfen Sie, ob die Dateien mit `Content-Encoding: br` oder `Content-Encoding: gz` bereitgestellt werden. Wenn der Host keine komprimierten Dateien bereitstellt, befolgen Sie die Anweisungen in <xref:blazor/host-and-deploy/webassembly#compression>.

### <a name="disable-unused-features"></a>Deaktivieren nicht genutzter Features

Die Blazor WebAssembly-Runtime schließt die folgenden .NET-Features ein, die deaktiviert werden können, wenn die App sie aufgrund einer kleineren Nutzlastgröße nicht benötigt:

* Eine Datendatei wird eingeschlossen, damit die Zeitzoneninformationen korrekt sind. Wenn die App dieses Feature nicht benötigt, ziehen Sie in Betracht, es zu deaktivieren, indem Sie die `BlazorEnableTimeZoneSupport`-MSBuild-Eigenschaft in der Projektdatei der App auf `false` festlegen:

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* Blazor WebAssembly enthält standardmäßig Globalisierungsressourcen, die zum Anzeigen von Werten wie Datums- und Währungsangaben in der Kultur des Benutzers erforderlich sind. Wenn für die App keine Lokalisierung erforderlich ist, können Sie [die App so konfigurieren, dass die invariante Kultur unterstützt wird](xref:blazor/globalization-localization), die auf der `en-US`-Kultur basiert:

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Die Sortierungsinformationen werden eingeschlossen, damit APIs wie <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> ordnungsgemäß funktionieren. Wenn Sie sicher sind, dass die App die Sortierungsinformationen nicht benötigt, ziehen Sie in Betracht, sie zu deaktivieren, indem Sie die `BlazorWebAssemblyPreserveCollationData`-MSBuild-Eigenschaft in der Projektdatei der App auf `false` festlegen:

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
