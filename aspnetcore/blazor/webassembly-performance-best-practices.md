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
# <a name="aspnet-core-no-locblazor-webassembly-performance-best-practices"></a><span data-ttu-id="5c45f-103">Best Practices zur Blazor WebAssembly-Leistung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5c45f-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="5c45f-104">Von [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="5c45f-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="5c45f-105">In diesem Artikel finden Sie Richtlinien zu Best Practices für die Leistung von Blazor WebAssembly-Apps in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="5c45f-105">This article provides guidelines for ASP.NET Core Blazor WebAssembly performance best practices.</span></span>

## <a name="avoid-unnecessary-component-renders"></a><span data-ttu-id="5c45f-106">Vermeiden von unnötigem Komponentenrendering</span><span class="sxs-lookup"><span data-stu-id="5c45f-106">Avoid unnecessary component renders</span></span>

<span data-ttu-id="5c45f-107">Der Vergleichsalgorithmus von Blazor verhindert das Rendern einer Komponente, wenn der Algorithmus erkennt, dass die Komponente nicht geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="5c45f-107">Blazor's diffing algorithm avoids rerendering a component when the algorithm perceives that the component hasn't changed.</span></span> <span data-ttu-id="5c45f-108">Überschreiben Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType>, wenn Sie eine genaue Kontrolle über das Rendern von Komponenten haben möchten.</span><span class="sxs-lookup"><span data-stu-id="5c45f-108">Override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A?displayProperty=nameWithType> for fine-grained control over component rendering.</span></span>

<span data-ttu-id="5c45f-109">Wenn eine nur auf die Benutzeroberfläche bezogene Komponente erstellt wird, die sich nach dem ursprünglichen Rendern nicht mehr ändert, konfigurieren Sie <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> so, dass `false` zurückgegeben wird:</span><span class="sxs-lookup"><span data-stu-id="5c45f-109">If authoring a UI-only component that never changes after the initial render, configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="5c45f-110">Für die meisten Apps ist eine genaue Kontrolle nicht erforderlich, aber <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> kann verwendet werden, um eine Komponente, die auf ein Benutzeroberflächenereignis antwortet, selektiv zu rendern.</span><span class="sxs-lookup"><span data-stu-id="5c45f-110">Most apps don't require fine-grained control, but <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> can be used to selectively render a component responding to a UI event.</span></span> <span data-ttu-id="5c45f-111">Die Verwendung von <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> könnte auch für Szenarien wichtig sein, in denen eine große Anzahl von Komponenten gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="5c45f-111">Using <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> might also be important in scenarios where a large number of components are rendered.</span></span> <span data-ttu-id="5c45f-112">Stellen Sie sich ein Raster vor, bei dem die Verwendung von <xref:Microsoft.AspNetCore.Components.EventCallback> in einer Komponente in einer Zelle des Rasters <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> im Raster aufruft.</span><span class="sxs-lookup"><span data-stu-id="5c45f-112">Consider a grid, where use of <xref:Microsoft.AspNetCore.Components.EventCallback> in one component in one cell of the grid calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on the grid.</span></span> <span data-ttu-id="5c45f-113">Das Aufrufen von <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> bewirkt, dass alle untergeordneten Komponenten erneut gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="5c45f-113">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> causes a re-render of every child component.</span></span> <span data-ttu-id="5c45f-114">Wenn nur eine kleine Anzahl von Zellen erneut gerendert werden muss, vermeiden Sie mit <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> Leistungseinbußen durch unnötige Renderings.</span><span class="sxs-lookup"><span data-stu-id="5c45f-114">If only a small number of cells require rerendering, use <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to avoid the performance penalty of unnecessary renders.</span></span>

<span data-ttu-id="5c45f-115">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5c45f-115">In the following example:</span></span>

* <span data-ttu-id="5c45f-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wird überschrieben und auf den Wert des <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>-Felds festgelegt, der ursprünglich `false` ist, wenn die Komponente geladen wird.</span><span class="sxs-lookup"><span data-stu-id="5c45f-116"><xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is overridden and set to the value of the <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> field, which is initially `false` when the component loads.</span></span>
* <span data-ttu-id="5c45f-117">Wenn auf die Schaltfläche geklickt wird, wird <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> auf `true` festgelegt, was erzwingt, dass die Komponente noch mal mit aktualisierter `currentCount`-Eigenschaft gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="5c45f-117">When the button is selected, <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> is set to `true`, which forces the component to rerender with the updated `currentCount`.</span></span>
* <span data-ttu-id="5c45f-118">Unmittelbar nach dem erneuten Rendern legt <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> den Wert von <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> wieder auf `false` fest, um zukünftiges erneutes Rendern bis zum nächsten Klick auf die Schaltfläche zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="5c45f-118">Immediately after rerendering, <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> sets the value of <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> back to `false` to prevent further rerendering until the next time the button is selected.</span></span>

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

<span data-ttu-id="5c45f-119">Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle#after-component-render>.</span><span class="sxs-lookup"><span data-stu-id="5c45f-119">For more information, see <xref:blazor/components/lifecycle#after-component-render>.</span></span>

## <a name="virtualize-re-usable-fragments"></a><span data-ttu-id="5c45f-120">Virtualisieren wiederverwendbarer Fragmente</span><span class="sxs-lookup"><span data-stu-id="5c45f-120">Virtualize re-usable fragments</span></span>

<span data-ttu-id="5c45f-121">Komponenten stellen einen bequemen Ansatz dar, um wiederverwendbare Code- und Markupfragmente zu generieren.</span><span class="sxs-lookup"><span data-stu-id="5c45f-121">Components offer a convenient approach to produce re-usable fragments of code and markup.</span></span> <span data-ttu-id="5c45f-122">Allgemein wird empfohlen, einzelne Komponenten zu erstellen, die am besten die Anforderungen der App erfüllen können.</span><span class="sxs-lookup"><span data-stu-id="5c45f-122">In general, we recommend authoring individual components that best align with the app's requirements.</span></span> <span data-ttu-id="5c45f-123">Eine Einschränkung besteht darin, dass die einzelnen zusätzlichen untergeordneten Komponenten zur Gesamtzeit beitragen, die benötigt wird, eine übergeordnete Komponente zu rendern.</span><span class="sxs-lookup"><span data-stu-id="5c45f-123">One caveat is that each additional child component contributes to the total time it takes to render a parent component.</span></span> <span data-ttu-id="5c45f-124">Bei den meisten Apps kann dieser zusätzliche Aufwand vernachlässigt werden.</span><span class="sxs-lookup"><span data-stu-id="5c45f-124">For most apps, the additional overhead is negligible.</span></span> <span data-ttu-id="5c45f-125">Für Apps, die eine große Anzahl Komponenten erzeugen, sollte die Verwendung von Strategien in Betracht gezogen werden, die den Verarbeitungsaufwand reduzieren, z. B. indem die Anzahl gerenderter Komponenten begrenzt wird.</span><span class="sxs-lookup"><span data-stu-id="5c45f-125">Apps that produce a large number of components should consider using strategies to reduce processing overhead, such as limiting the number of rendered components.</span></span>

<span data-ttu-id="5c45f-126">Bei einem Raster oder einer Liste, die Hunderte Datensätze rendert, die Komponenten enthalten, ist die Auslastung des Prozessors für das Rendern beispielsweise höher.</span><span class="sxs-lookup"><span data-stu-id="5c45f-126">For example, a grid or list that renders hundreds of rows containing components is processor intensive to render.</span></span> <span data-ttu-id="5c45f-127">Ziehen Sie es in Betracht, ein Raster oder ein Listenlayout zu virtualisieren, sodass nur eine Teilmenge der Komponenten jeweils gleichzeitig gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="5c45f-127">Consider virtualizing a grid or list layout so that only a subset of the components is rendered at any given time.</span></span> <span data-ttu-id="5c45f-128">Ein Beispiel für das Rendern einer Teilmenge von Komponenten finden Sie bei den folgenden Komponenten unter [`Virtualization`-Beispiel-App (aspnet/samples, GitHub-Repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span><span class="sxs-lookup"><span data-stu-id="5c45f-128">For an example of component subset rendering, see the following components in the [`Virtualization` sample app (aspnet/samples GitHub repository)](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor/Virtualization):</span></span>

* <span data-ttu-id="5c45f-129">`Virtualize`-Komponente ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): Eine in C# geschriebene Komponente, die <xref:Microsoft.AspNetCore.Components.ComponentBase> implementiert, um mehrere Wetterdatensätze basierend auf dem Scrollen des Benutzers zu rendern</span><span class="sxs-lookup"><span data-stu-id="5c45f-129">`Virtualize` component ([`Shared/Virtualize.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Shared/Virtualize.cs)): A component written in C# that implements <xref:Microsoft.AspNetCore.Components.ComponentBase> to render a set of weather data rows based on user scrolling.</span></span>
* <span data-ttu-id="5c45f-130">`FetchData`-Komponente ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Verwendet die `Virtualize`-Komponente, um 25 Wetterdatensätze gleichzeitig anzuzeigen</span><span class="sxs-lookup"><span data-stu-id="5c45f-130">`FetchData` component ([`Pages/FetchData.razor`](https://github.com/aspnet/samples/blob/master/samples/aspnetcore/blazor/Virtualization/Pages/FetchData.razor)): Uses the `Virtualize` component to display 25 rows of weather data at a time.</span></span>

## <a name="avoid-javascript-interop-to-marshal-data"></a><span data-ttu-id="5c45f-131">Vermeiden von JavaScript-Interoperabilität zum Marshallen von Daten</span><span class="sxs-lookup"><span data-stu-id="5c45f-131">Avoid JavaScript interop to marshal data</span></span>

<span data-ttu-id="5c45f-132">In Blazor WebAssembly muss ein JavaScript-Interop-Aufruf (JS) die WebAssembly-JS-Grenze überschreiten.</span><span class="sxs-lookup"><span data-stu-id="5c45f-132">In Blazor WebAssembly, a JavaScript (JS) interop call must traverse the WebAssembly-JS boundary.</span></span> <span data-ttu-id="5c45f-133">Das Serialisieren und Deserialisieren von Inhalt für die beiden Kontexte führt zu Verarbeitungsaufwand für die App.</span><span class="sxs-lookup"><span data-stu-id="5c45f-133">Serializing and deserializing content across the two contexts creates processing overhead for the app.</span></span> <span data-ttu-id="5c45f-134">Häufige JS-Interop-Aufrufe beeinträchtigen die Leistung oftmals.</span><span class="sxs-lookup"><span data-stu-id="5c45f-134">Frequent JS interop calls often adversely affects performance.</span></span> <span data-ttu-id="5c45f-135">Bestimmen Sie, ob die App viele kleine Nutzlasten in eine einzelne große Payload konsolidieren kann, um das Marshallen der Daten über die Grenze hinweg zu reduzieren, indem das hohe Volumen des Kontextwechselns zwischen WebAssembly und JS vermieden wird.</span><span class="sxs-lookup"><span data-stu-id="5c45f-135">To reduce the marshalling of data across the boundary, determine if the app can consolidate many small payloads into a single large payload to avoid the high volume of context switching between WebAssembly and JS.</span></span>

## <a name="use-systemtextjson"></a><span data-ttu-id="5c45f-136">Verwenden von System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="5c45f-136">Use System.Text.Json</span></span>

<span data-ttu-id="5c45f-137">Die JS-Interop-Implementierung von Blazor basiert auf <xref:System.Text.Json>. Dabei handelt es sich um eine JSON-Hochleistungsserialisierungsbibliothek mit niedriger Arbeitsspeicherzuteilung.</span><span class="sxs-lookup"><span data-stu-id="5c45f-137">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="5c45f-138">Das Verwenden von <xref:System.Text.Json> führt nicht zu einer zusätzlichen Nutzlastgröße für die App, wenn eine oder mehrere alternative JSON-Bibliotheken hinzufügt werden.</span><span class="sxs-lookup"><span data-stu-id="5c45f-138">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="5c45f-139">Eine Anleitung zur Migration finden Sie unter [Migration von `Newtonsoft.Json` zu `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span><span class="sxs-lookup"><span data-stu-id="5c45f-139">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

## <a name="use-synchronous-and-unmarshalled-js-interop-apis-where-appropriate"></a><span data-ttu-id="5c45f-140">Verwenden von synchronen und nicht gemarshallten JS-Interop-APIs (wenn möglich)</span><span class="sxs-lookup"><span data-stu-id="5c45f-140">Use synchronous and unmarshalled JS interop APIs where appropriate</span></span>

<span data-ttu-id="5c45f-141">Blazor WebAssembly bietet zwei zusätzliche Versionen von <xref:Microsoft.JSInterop.IJSRuntime> zusätzlich zur einzelnen für Blazor Server-Apps verfügbaren Version:</span><span class="sxs-lookup"><span data-stu-id="5c45f-141">Blazor WebAssembly offers two additional versions of <xref:Microsoft.JSInterop.IJSRuntime> over the single version available to Blazor Server apps:</span></span>

* <span data-ttu-id="5c45f-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> ermöglicht das synchrone Aufrufen von JS-Interop-Aufrufen, was im Vergleich zu asynchronen Versionen zu weniger Aufwand führt:</span><span class="sxs-lookup"><span data-stu-id="5c45f-142"><xref:Microsoft.JSInterop.IJSInProcessRuntime> allows invoking JS interop calls synchronously, which has less overhead than the asynchronous versions:</span></span>

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

* <span data-ttu-id="5c45f-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> ermöglicht nicht gemarshallte JS-Interop-Aufrufe:</span><span class="sxs-lookup"><span data-stu-id="5c45f-143"><xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> permits unmarshalled JS interop calls:</span></span>

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
  > <span data-ttu-id="5c45f-144">Während die Verwendung von <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> den geringsten Aufwand der JS-Interop-Ansätze hat, gibt es für die JavaScript-APIs, die erforderlich sind, um mit diesen APIs zu interagieren, aktuell keine Dokumentation. Außerdem werden in diesem Bereich im Rahmen zukünftiger Releases Breaking Changes vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="5c45f-144">While using <xref:Microsoft.JSInterop.WebAssembly.WebAssemblyJSRuntime> has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

## <a name="reduce-app-size"></a><span data-ttu-id="5c45f-145">Reduzieren der App-Größe</span><span class="sxs-lookup"><span data-stu-id="5c45f-145">Reduce app size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="5c45f-146">IL-Kürzung (Intermediate Language, Zwischensprache)</span><span class="sxs-lookup"><span data-stu-id="5c45f-146">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="5c45f-147">[Das Kürzen nicht verwendeter Assemblys über eine Blazor WebAssembly-App](xref:blazor/host-and-deploy/configure-trimmer) reduziert die Größe der App, indem nicht genutzter Code in den Binärdateien der App entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="5c45f-147">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="5c45f-148">Der Trimmer wird standardmäßig beim Veröffentlichen einer Anwendung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5c45f-148">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="5c45f-149">Veröffentlichen Sie die App für die Bereitstellung mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) mit auf `Release` festgelegter Option [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), um von der Kürzung zu profitieren:</span><span class="sxs-lookup"><span data-stu-id="5c45f-149">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="5c45f-150">Intermediate Language-Verknüpfung (IL)</span><span class="sxs-lookup"><span data-stu-id="5c45f-150">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="5c45f-151">[Das Konfigurieren eines Linkers für eine Blazor WebAssembly-App](xref:blazor/host-and-deploy/configure-linker) reduziert die Größe der App, indem nicht genutzter Code in den Binärdateien der App gekürzt wird.</span><span class="sxs-lookup"><span data-stu-id="5c45f-151">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="5c45f-152">Standardmäßig ist der IL-Linker (Intermediate Language, Zwischensprache) nur aktiviert, wenn der Erstellvorgang in der `Release`-Konfiguration ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5c45f-152">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="5c45f-153">Veröffentlichen Sie die App für die Bereitstellung mithilfe des Befehls [`dotnet publish`](/dotnet/core/tools/dotnet-publish) mit auf `Release` festgelegter Option [-c|--configuration](/dotnet/core/tools/dotnet-publish#options), um diesen Vorteil zu nutzen:</span><span class="sxs-lookup"><span data-stu-id="5c45f-153">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="lazy-load-assemblies"></a><span data-ttu-id="5c45f-154">Lazy Loading-Assemblys</span><span class="sxs-lookup"><span data-stu-id="5c45f-154">Lazy load assemblies</span></span>

<span data-ttu-id="5c45f-155">Laden Sie Assemblys zur Laufzeit, wenn die Assemblys von einer Route benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="5c45f-155">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="5c45f-156">Weitere Informationen finden Sie unter <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="5c45f-156">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="5c45f-157">Komprimierung</span><span class="sxs-lookup"><span data-stu-id="5c45f-157">Compression</span></span>

<span data-ttu-id="5c45f-158">Wenn eine Blazor WebAssembly-App veröffentlicht wird, wird die Ausgabe bei der Veröffentlichung statisch komprimiert, um die App-Größe zu verringern und den Aufwand für eine Laufzeitkomprimierung zu beseitigen.</span><span class="sxs-lookup"><span data-stu-id="5c45f-158">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="5c45f-159">Blazor basiert darauf, dass der Server eine Inhaltsaushandlung ausführt und statisch komprimierte Dateien bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="5c45f-159">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="5c45f-160">Nach der Bereitstellung einer App überprüfen Sie, ob die App komprimierte Dateien bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="5c45f-160">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="5c45f-161">Sehen Sie sich die Netzwerkregisterkarte der Entwicklertools eines Browsers an, und überprüfen Sie, ob die Dateien mit `Content-Encoding: br` oder `Content-Encoding: gz` bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="5c45f-161">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="5c45f-162">Wenn der Host keine komprimierten Dateien bereitstellt, befolgen Sie die Anweisungen in <xref:blazor/host-and-deploy/webassembly#compression>.</span><span class="sxs-lookup"><span data-stu-id="5c45f-162">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="5c45f-163">Deaktivieren nicht genutzter Features</span><span class="sxs-lookup"><span data-stu-id="5c45f-163">Disable unused features</span></span>

<span data-ttu-id="5c45f-164">Die Blazor WebAssembly-Runtime schließt die folgenden .NET-Features ein, die deaktiviert werden können, wenn die App sie aufgrund einer kleineren Nutzlastgröße nicht benötigt:</span><span class="sxs-lookup"><span data-stu-id="5c45f-164">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="5c45f-165">Eine Datendatei wird eingeschlossen, damit die Zeitzoneninformationen korrekt sind.</span><span class="sxs-lookup"><span data-stu-id="5c45f-165">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="5c45f-166">Wenn die App dieses Feature nicht benötigt, ziehen Sie in Betracht, es zu deaktivieren, indem Sie die `BlazorEnableTimeZoneSupport`-MSBuild-Eigenschaft in der Projektdatei der App auf `false` festlegen:</span><span class="sxs-lookup"><span data-stu-id="5c45f-166">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="5c45f-167">Blazor WebAssembly enthält standardmäßig Globalisierungsressourcen, die zum Anzeigen von Werten wie Datums- und Währungsangaben in der Kultur des Benutzers erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="5c45f-167">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="5c45f-168">Wenn für die App keine Lokalisierung erforderlich ist, können Sie [die App so konfigurieren, dass die invariante Kultur unterstützt wird](xref:blazor/globalization-localization), die auf der `en-US`-Kultur basiert:</span><span class="sxs-lookup"><span data-stu-id="5c45f-168">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="5c45f-169">Die Sortierungsinformationen werden eingeschlossen, damit APIs wie <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="5c45f-169">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="5c45f-170">Wenn Sie sicher sind, dass die App die Sortierungsinformationen nicht benötigt, ziehen Sie in Betracht, sie zu deaktivieren, indem Sie die `BlazorWebAssemblyPreserveCollationData`-MSBuild-Eigenschaft in der Projektdatei der App auf `false` festlegen:</span><span class="sxs-lookup"><span data-stu-id="5c45f-170">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
