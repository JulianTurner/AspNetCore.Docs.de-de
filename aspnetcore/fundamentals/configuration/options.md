---
title: Optionsmuster in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Optionsmuster verwenden, um Gruppen von zusammengehörigen Einstellungen in ASP.NET Core-Anwendungen darzustellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
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
uid: fundamentals/configuration/options
ms.openlocfilehash: 6eafad63bd4c49cee6a85108b37a8b61e1214bce
ms.sourcegitcommit: d7991068bc6b04063f4bd836fc5b9591d614d448
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91762333"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="d9164-103">Optionsmuster in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d9164-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d9164-104">Von [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d9164-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="d9164-105">Das Optionsmuster verwendet Klassen, um stark typisierten Zugriff auf zusammengehörige Einstellungsgruppen zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="d9164-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="d9164-106">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="d9164-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="d9164-107">Das [Schnittstellentrennungsprinzip oder die Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="d9164-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="d9164-108">[Trennung von Zuständigkeiten:](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) Einstellungen für verschiedene App-Komponenten sind nicht voneinander abhängig und nicht aneinander gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="d9164-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="d9164-109">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="d9164-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="d9164-110">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="d9164-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="d9164-111">Dieses Thema enthält Informationen zu den Optionsmustern in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d9164-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="d9164-112">Informationen zur Verwendung des Optionsmusters in Konsolen-Apps finden Sie unter [Optionsmuster in .NET](/dotnet/core/extensions/options).</span><span class="sxs-lookup"><span data-stu-id="d9164-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="d9164-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d9164-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="d9164-114">Binden von hierarchischen Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="d9164-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="d9164-115">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="d9164-115">Options interfaces</span></span>

<span data-ttu-id="d9164-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="d9164-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="d9164-117">Unterstützt ***nicht***:</span><span class="sxs-lookup"><span data-stu-id="d9164-117">Does ***not*** support:</span></span>
  * <span data-ttu-id="d9164-118">Das Lesen von Konfigurationsdaten, nachdem die App gestartet wurde</span><span class="sxs-lookup"><span data-stu-id="d9164-118">Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="d9164-119">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="d9164-119">Named options</span></span>](#named)
* <span data-ttu-id="d9164-120">Ist als [Singleton](xref:fundamentals/dependency-injection#singleton) registriert und kann in eine beliebige [Dienstlebensdauer](xref:fundamentals/dependency-injection#service-lifetimes) eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="d9164-120">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="d9164-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="d9164-121"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="d9164-122">Ist in Szenarios nützlich, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="d9164-122">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="d9164-123">Weitere Informationen finden Sie unter [Verwenden von IOptionsSnapshot zum Lesen aktualisierter Daten](#ios).</span><span class="sxs-lookup"><span data-stu-id="d9164-123">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="d9164-124">Ist als [Bereichsbezogen](xref:fundamentals/dependency-injection#scoped) registriert und kann deshalb nicht in einen Singleton-Dienst eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-124">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="d9164-125">Unterstützt [benannten Optionen](#named)</span><span class="sxs-lookup"><span data-stu-id="d9164-125">Supports [named options](#named)</span></span>

<span data-ttu-id="d9164-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="d9164-126"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="d9164-127">Wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten</span><span class="sxs-lookup"><span data-stu-id="d9164-127">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="d9164-128">Ist als [Singleton](xref:fundamentals/dependency-injection#singleton) registriert und kann in eine beliebige [Dienstlebensdauer](xref:fundamentals/dependency-injection#service-lifetimes) eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="d9164-128">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="d9164-129">Unterstützt:</span><span class="sxs-lookup"><span data-stu-id="d9164-129">Supports:</span></span>
  * <span data-ttu-id="d9164-130">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="d9164-130">Change notifications</span></span>
  * [<span data-ttu-id="d9164-131">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="d9164-131">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="d9164-132">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d9164-132">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="d9164-133">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="d9164-133">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="d9164-134">Mit [Postkonfigurationsszenarios](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="d9164-134">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="d9164-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="d9164-135"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="d9164-136">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="d9164-136">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="d9164-137">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="d9164-137">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="d9164-138">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="d9164-138">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="d9164-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="d9164-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="d9164-140"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="d9164-140">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="d9164-141">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-141">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="d9164-142">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="d9164-142">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="d9164-143">Verwenden von IOptionsSnapshot zum Lesen aktualisierter Daten</span><span class="sxs-lookup"><span data-stu-id="d9164-143">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="d9164-144">Mithilfe von <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> werden Optionen einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-144">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="d9164-145">Änderungen an der Konfiguration werden gelesen, nachdem die App gestartet wurde, wenn Konfigurationsanbieter verwendet werden, die das Lesen aktualisierter Konfigurationswerte unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d9164-145">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="d9164-146">Der Unterschied zwischen `IOptionsMonitor` und `IOptionsSnapshot` ist folgender:</span><span class="sxs-lookup"><span data-stu-id="d9164-146">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="d9164-147">`IOptionsMonitor` ist ein [Singleton-Dienst](xref:fundamentals/dependency-injection#singleton), der zu jeder Zeit aktuelle Optionswerte empfängt, was insbesondere bei Singleton-Abhängigkeiten nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="d9164-147">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="d9164-148">`IOptionsSnapshot` ist ein [bereichsbezogener Dienst](xref:fundamentals/dependency-injection#scoped) und bietet eine Momentaufnahme der Optionen zu dem Zeitpunkt, da das `IOptionsSnapshot<T>`-Objekt konstruiert wird.</span><span class="sxs-lookup"><span data-stu-id="d9164-148">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="d9164-149">Momentaufnahmen von Optionen sind für die Verwendung mit vorübergehenden und bereichsbezogenen Abhängigkeiten bestimmt.</span><span class="sxs-lookup"><span data-stu-id="d9164-149">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="d9164-150">Der folgende Code verwendet <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span><span class="sxs-lookup"><span data-stu-id="d9164-150">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="d9164-151">Der folgende Code registriert eine Konfigurationsinstanz, mit der `MyOptions` eine Bindung herstellt:</span><span class="sxs-lookup"><span data-stu-id="d9164-151">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="d9164-152">Im vorangehenden Code werden Änderungen an der JSON-Konfigurationsdatei nach dem Start der App gelesen.</span><span class="sxs-lookup"><span data-stu-id="d9164-152">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="d9164-153">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="d9164-153">IOptionsMonitor</span></span>

<span data-ttu-id="d9164-154">Der folgende Code registriert eine Konfigurationsinstanz, mit der `MyOptions` eine Bindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="d9164-154">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="d9164-155">Im folgenden Beispiel wird <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> verwendet:</span><span class="sxs-lookup"><span data-stu-id="d9164-155">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="d9164-156">Im vorangehenden Code werden standardmäßig Änderungen an der JSON-Konfigurationsdatei gelesen, nachdem die App gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="d9164-156">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="d9164-157">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="d9164-157">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="d9164-158">Benannte Optionen...</span><span class="sxs-lookup"><span data-stu-id="d9164-158">Named options:</span></span>

* <span data-ttu-id="d9164-159">sind nützlich, wenn mehrere Konfigurationsabschnitte an die gleichen Eigenschaften gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-159">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="d9164-160">unterscheiden zwischen Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="d9164-160">Are case sensitive.</span></span>

<span data-ttu-id="d9164-161">Sehen Sie sich die nachfolgende Datei *appsettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="d9164-161">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="d9164-162">Anstatt zwei Klassen für die Bindung von `TopItem:Month` und `TopItem:Year` zu erstellen, wird folgende Klasse für jeden Abschnitt verwendet:</span><span class="sxs-lookup"><span data-stu-id="d9164-162">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="d9164-163">Der folgende Code dient zum Konfigurieren der benannten Optionen:</span><span class="sxs-lookup"><span data-stu-id="d9164-163">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="d9164-164">Der folgende Code zeigt die benannten Optionen an:</span><span class="sxs-lookup"><span data-stu-id="d9164-164">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="d9164-165">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="d9164-165">All options are named instances.</span></span> <span data-ttu-id="d9164-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als würden sie die `Options.DefaultName`-Instanz anzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="d9164-166"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="d9164-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="d9164-167"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="d9164-168">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d9164-168">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="d9164-169">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen.</span><span class="sxs-lookup"><span data-stu-id="d9164-169">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="d9164-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention.</span><span class="sxs-lookup"><span data-stu-id="d9164-170"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="d9164-171">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="d9164-171">OptionsBuilder API</span></span>

<span data-ttu-id="d9164-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="d9164-172"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="d9164-173">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="d9164-173">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="d9164-174">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d9164-174">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="d9164-175">`OptionsBuilder` wird im Abschnitt [Überprüfung von Optionen](#val) verwendet.</span><span class="sxs-lookup"><span data-stu-id="d9164-175">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="d9164-176">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="d9164-176">Use DI services to configure options</span></span>

<span data-ttu-id="d9164-177">Der Zugriff auf Dienste ist über die Dependency Injection möglich, während Optionen auf zwei Arten konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="d9164-177">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="d9164-178">Übergeben Sie einen Konfigurationsdelegaten an [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="d9164-178">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="d9164-179">`OptionsBuilder<TOptions>` stellt Überladungen von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="d9164-179">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="d9164-180">Erstellen Sie einen Typ, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und registrieren Sie den Typ als Dienst.</span><span class="sxs-lookup"><span data-stu-id="d9164-180">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="d9164-181">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="d9164-181">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="d9164-182">Das Erstellen eines Typs entspricht den Aktionen des Frameworks beim Aufruf von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="d9164-182">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="d9164-183">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="d9164-183">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="d9164-184">Überprüfung von Optionen</span><span class="sxs-lookup"><span data-stu-id="d9164-184">Options validation</span></span>

<span data-ttu-id="d9164-185">Bei der Überprüfung der Optionen können Optionswerte überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-185">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="d9164-186">Sehen Sie sich die nachfolgende Datei *appsettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="d9164-186">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="d9164-187">Die folgende Klasse erstellt eine Bindung zum `"MyConfig"`-Konfigurationsabschnitt und wendet einige `DataAnnotations`-Regeln an:</span><span class="sxs-lookup"><span data-stu-id="d9164-187">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="d9164-188">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="d9164-188">The following code:</span></span>

* <span data-ttu-id="d9164-189">Mit dem Code wird die <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A>-Methode abgerufen, um eine [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)-Klasse zu erhalten, die an die `MyConfigOptions`-Klasse gebunden wird.</span><span class="sxs-lookup"><span data-stu-id="d9164-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="d9164-190">Mit dem Code wird <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> abgerufen, um die Validierung mithilfe von `DataAnnotations` zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="d9164-190">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="d9164-191">Die `ValidateDataAnnotations`-Erweiterungsmethode wird im NuGet-Paket [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) definiert.</span><span class="sxs-lookup"><span data-stu-id="d9164-191">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="d9164-192">Für Web-Apps, die das `Microsoft.NET.Sdk.Web` SDK verwenden, wird auf dieses Paket implizit über das geteilte Framework verwiesen.</span><span class="sxs-lookup"><span data-stu-id="d9164-192">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="d9164-193">Der folgende Code zeigt die Konfigurationswerte oder die Überprüfungsfehler an:</span><span class="sxs-lookup"><span data-stu-id="d9164-193">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="d9164-194">Der folgende Code wendet mithilfe eines Delegaten eine komplexere Überprüfungsregel an:</span><span class="sxs-lookup"><span data-stu-id="d9164-194">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="d9164-195">IValidateOptions für die komplexe Überprüfung</span><span class="sxs-lookup"><span data-stu-id="d9164-195">IValidateOptions for complex validation</span></span>

<span data-ttu-id="d9164-196">Die folgende Klasse implementiert <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span><span class="sxs-lookup"><span data-stu-id="d9164-196">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="d9164-197">`IValidateOptions` ermöglicht das Verschieben des Überprüfungscodes aus `StartUp` und in eine Klasse.</span><span class="sxs-lookup"><span data-stu-id="d9164-197">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="d9164-198">Mit dem vorangehenden Code wird die Überprüfung in `Startup.ConfigureServices` aktiviert:</span><span class="sxs-lookup"><span data-stu-id="d9164-198">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/StartupValidation.cs?name=snippet)]

<!-- The following comment doesn't seem that useful 
Options validation doesn't guard against options modifications after the options instance is created. For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed. The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.

The `Validate` method accepts a `Func<TOptions, bool>`. To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:

* Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`
* Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`

`IValidateOptions` validates:

* A specific named options instance.
* All options when `name` is `null`.

Return a `ValidateOptionsResult` from your implementation of the interface:

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`. `Microsoft.Extensions.Options.DataAnnotations` is implicitly referenced in ASP.NET Core apps.

-->

## <a name="options-post-configuration"></a><span data-ttu-id="d9164-199">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="d9164-199">Options post-configuration</span></span>

<span data-ttu-id="d9164-200">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="d9164-200">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="d9164-201">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="d9164-201">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d9164-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="d9164-202"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d9164-203">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="d9164-203">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="d9164-204">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="d9164-204">Accessing options during startup</span></span>

<span data-ttu-id="d9164-205"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d9164-205"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="d9164-206">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d9164-206">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d9164-207">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="d9164-207">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="d9164-208">NuGet-Paket Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="d9164-208">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="d9164-209">Auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) wird implizit in ASP.NET Core-Apps verwiesen.</span><span class="sxs-lookup"><span data-stu-id="d9164-209">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d9164-210">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="d9164-210">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="d9164-211">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="d9164-211">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="d9164-212">Das [Schnittstellentrennungsprinzip oder die Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="d9164-212">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="d9164-213">[Trennung von Zuständigkeiten:](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) Einstellungen für verschiedene App-Komponenten sind nicht voneinander abhängig und nicht aneinander gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="d9164-213">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="d9164-214">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="d9164-214">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="d9164-215">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="d9164-215">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="d9164-216">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d9164-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d9164-217">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d9164-217">Prerequisites</span></span>

<span data-ttu-id="d9164-218">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="d9164-218">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="d9164-219">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="d9164-219">Options interfaces</span></span>

<span data-ttu-id="d9164-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="d9164-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="d9164-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="d9164-221"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="d9164-222">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="d9164-222">Change notifications</span></span>
* [<span data-ttu-id="d9164-223">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="d9164-223">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="d9164-224">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d9164-224">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="d9164-225">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="d9164-225">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="d9164-226">Mit [Postkonfigurationsszenarien](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="d9164-226">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="d9164-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="d9164-227"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="d9164-228">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="d9164-228">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="d9164-229">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="d9164-229">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="d9164-230">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="d9164-230">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="d9164-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="d9164-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="d9164-232"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="d9164-232">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="d9164-233">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-233">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="d9164-234">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="d9164-234">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="d9164-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ist nützlich in Szenarien, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="d9164-235"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="d9164-236">Weitere Informationen finden Sie im Abschnitt [Neuladen der Konfigurationsdaten mit IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="d9164-236">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="d9164-237"><xref:Microsoft.Extensions.Options.IOptions%601> kann zum Unterstützen von Optionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-237"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="d9164-238">Allerdings unterstützt <xref:Microsoft.Extensions.Options.IOptions%601> nicht die oben beschriebenen Szenarios von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="d9164-238">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="d9164-239">Sie können <xref:Microsoft.Extensions.Options.IOptions%601> weiterhin in bestehenden Frameworks und Bibliotheken verwenden, die bereits die <xref:Microsoft.Extensions.Options.IOptions%601>-Schnittstelle verwenden und nicht die von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bereitgestellten Szenarios benötigen.</span><span class="sxs-lookup"><span data-stu-id="d9164-239">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="d9164-240">Allgemeine Optionskonfiguration</span><span class="sxs-lookup"><span data-stu-id="d9164-240">General options configuration</span></span>

<span data-ttu-id="d9164-241">Die allgemeine Optionskonfiguration wird als Beispiel 1 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-241">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="d9164-242">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="d9164-242">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="d9164-243">Die folgende Klasse `MyOptions` verfügt über die zwei Eigenschaften: `Option1` und `Option2`.</span><span class="sxs-lookup"><span data-stu-id="d9164-243">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="d9164-244">Das Festlegen von Standardwerten ist optional, aber der Klassenkonstruktor im folgenden Beispiel legt den Standardwert von `Option1` fest.</span><span class="sxs-lookup"><span data-stu-id="d9164-244">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="d9164-245">`Option2` hat den Standardwert festgelegt, indem die Eigenschaft direkt initialisiert wurde (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-245">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="d9164-246">Die `MyOptions`-Klasse wird zum Dienstcontainer mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="d9164-246">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="d9164-247">Das folgende Seitenmodel verwendet [konstruktorbasierte Dependency Injection](xref:mvc/controllers/dependency-injection) mit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, um auf die Einstellungen zugreifen zu können (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-247">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="d9164-248">Die *appsettings.json*-Datei des Beispiels gibt Werte für `option1` und `option2` an:</span><span class="sxs-lookup"><span data-stu-id="d9164-248">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="d9164-249">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="d9164-249">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="d9164-250">Wenn Sie eine benutzerdefinierte <xref:System.Configuration.ConfigurationBuilder>-Klasse verwenden, um Konfigurationsoptionen aus einer Einstellungsdatei zu laden, bestätigen Sie, dass der Basispfad ordnungsgemäß festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="d9164-250">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="d9164-251">Sie müssen den Basispfad nicht explizit festlegen, wenn Sie Konfigurationsoptionen über <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aus der Einstellungsdatei laden.</span><span class="sxs-lookup"><span data-stu-id="d9164-251">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="d9164-252">Konfigurieren von einfachen Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="d9164-252">Configure simple options with a delegate</span></span>

<span data-ttu-id="d9164-253">Das Konfigurieren von einfachen Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-253">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="d9164-254">Verwenden Sie einen Delegaten zum Festlegen von Optionswerten.</span><span class="sxs-lookup"><span data-stu-id="d9164-254">Use a delegate to set options values.</span></span> <span data-ttu-id="d9164-255">Die Beispielanwendung verwendet die `MyOptionsWithDelegateConfig`-Klasse (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-255">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="d9164-256">Im folgenden Code wird ein zweiter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d9164-256">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d9164-257">Er verwendet einen Delegaten zum Konfigurieren der Bindung mit `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="d9164-257">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="d9164-258">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d9164-258">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="d9164-259">Sie können mehrere Konfigurationsanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d9164-259">You can add multiple configuration providers.</span></span> <span data-ttu-id="d9164-260">Konfigurationsanbieter sind über NuGet-Pakete verfügbar und werden in der Reihenfolge ihrer Registrierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="d9164-260">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="d9164-261">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d9164-261">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="d9164-262">Jeder Aufruf von <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> fügt einen <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzu.</span><span class="sxs-lookup"><span data-stu-id="d9164-262">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="d9164-263">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` beide in *appsettings.json* angegeben, aber die Werte von `Option1` und `Option2` werden vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="d9164-263">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="d9164-264">Wenn mehr als ein Konfigurationsdienst aktiviert ist, *gewinnt* die letzte angegebene Konfigurationsquelle und legt den Konfigurationswert fest.</span><span class="sxs-lookup"><span data-stu-id="d9164-264">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="d9164-265">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="d9164-265">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="d9164-266">Konfigurieren von Unteroptionen</span><span class="sxs-lookup"><span data-stu-id="d9164-266">Suboptions configuration</span></span>

<span data-ttu-id="d9164-267">Das Konfigurieren von Unteroptionen wird als Beispiel 3 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-267">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="d9164-268">Anwendungen sollten Optionsklassen erstellen, die für bestimmte Szenariogruppen (Klassen) in der Anwendung gelten.</span><span class="sxs-lookup"><span data-stu-id="d9164-268">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="d9164-269">Die Komponenten der Anwendung, die Konfigurationswerte erfordern, sollten nur über Zugriff auf die Konfigurationswerte verfügen, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="d9164-269">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="d9164-270">Beim Binden von Optionen zur Konfiguration ist jede Eigenschaft im Optionstyp an einen Konfigurationsschlüssel des `property[:sub-property:]`-Formulars gebunden.</span><span class="sxs-lookup"><span data-stu-id="d9164-270">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="d9164-271">Die `MyOptions.Option1`-Eigenschaft ist beispielsweise an den Schlüssel `Option1` gebunden, der von der `option1`-Eigenschaft in *appsettings.json* gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="d9164-271">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="d9164-272">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d9164-272">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d9164-273">Er verbindet `MySubOptions` mit dem `subsection`-Abschnitt der *appSettings.json*-Datei:</span><span class="sxs-lookup"><span data-stu-id="d9164-273">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="d9164-274">Die `GetSection`-Methode erfordert den Namespace <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d9164-274">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="d9164-275">Die *appsettings.json*-Datei des Beispiels definiert ein `subsection`-Element mit Schlüsseln für `suboption1` und `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="d9164-275">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="d9164-276">Die `MySubOptions`-Klasse definiert die Eigenschaften `SubOption1` und `SubOption2` zur Aufnahme der Optionswerte (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-276">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="d9164-277">Die `OnGet`-Methode des Seitenmodells gibt eine Zeichenfolge mit den Optionswerten (*Pages/Index.cshtml.cs*) zurück:</span><span class="sxs-lookup"><span data-stu-id="d9164-277">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="d9164-278">Wenn die App ausgeführt wird, gibt die `OnGet`-Methode eine Zeichenfolge zurück, die die Werte der untergeordneten Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="d9164-278">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="d9164-279">Einfügung von Optionen</span><span class="sxs-lookup"><span data-stu-id="d9164-279">Options injection</span></span>

<span data-ttu-id="d9164-280">Die Einfügung von Optionen ist als Beispiel 4 in der Beispiel-App dargestellt.</span><span class="sxs-lookup"><span data-stu-id="d9164-280">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="d9164-281">Fügen Sie <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ein in:</span><span class="sxs-lookup"><span data-stu-id="d9164-281">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="d9164-282">Eine Razor-Seite oder MVC-Ansicht mithilfe der [`@inject`](xref:mvc/views/razor#inject) Razor-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="d9164-282">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="d9164-283">Ein Seiten- oder Ansichtsmodell.</span><span class="sxs-lookup"><span data-stu-id="d9164-283">A page or view model.</span></span>

<span data-ttu-id="d9164-284">Im folgenden Beispiel aus der Beispiel-App wird <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in ein Seitenmodell eingefügt (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-284">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="d9164-285">Die Beispiel-App zeigt das Einfügen von `IOptionsMonitor<MyOptions>` mit einer `@inject`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="d9164-285">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="d9164-286">Wenn die App ausgeführt wird, werden die Optionswerte in der gerenderten Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d9164-286">When the app is run, the options values are shown in the rendered page:</span></span>

![Optionswerte Option1: value1_from_json und Option2: -1 werden aus dem Modell geladen und in die Ansicht eingefügt.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="d9164-288">Neuladen der Konfigurationsdaten mit IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="d9164-288">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="d9164-289">Das Neuladen der Konfigurationsdaten mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> wird im Beispiel 5 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-289">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="d9164-290">Mithilfe von <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> werden Optionen einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-290">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="d9164-291">Der Unterschied zwischen `IOptionsMonitor` und `IOptionsSnapshot` ist folgender:</span><span class="sxs-lookup"><span data-stu-id="d9164-291">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="d9164-292">`IOptionsMonitor` ist ein [Singleton-Dienst](xref:fundamentals/dependency-injection#singleton), der zu jeder Zeit aktuelle Optionswerte empfängt, was insbesondere bei Singleton-Abhängigkeiten nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="d9164-292">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="d9164-293">`IOptionsSnapshot` ist ein [bereichsbezogener Dienst](xref:fundamentals/dependency-injection#scoped) und bietet eine Momentaufnahme der Optionen zu dem Zeitpunkt, da das `IOptionsSnapshot<T>`-Objekt konstruiert wird.</span><span class="sxs-lookup"><span data-stu-id="d9164-293">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="d9164-294">Momentaufnahmen von Optionen sind für die Verwendung mit vorübergehenden und bereichsbezogenen Abhängigkeiten bestimmt.</span><span class="sxs-lookup"><span data-stu-id="d9164-294">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="d9164-295">Im folgenden Beispiel wird veranschaulicht, wie eine neue <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nach den *appsettings.json*-Veränderungen erstellt wird (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="d9164-295">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="d9164-296">Mehrere Anforderungen an den Server geben konstante Werte zurück, die von der *appsettings.json*-Datei bereitgestellt werden, bis die Datei geändert wird und die Konfiguration neu lädt.</span><span class="sxs-lookup"><span data-stu-id="d9164-296">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="d9164-297">Die folgende Abbildung zeigt die anfänglichen `option1`- und `option2`-Werte, die aus der *appsettings.json*-Datei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="d9164-297">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="d9164-298">Ändern Sie die Werte in der *appsettings.json*-Datei auf `value1_from_json UPDATED` und `200`.</span><span class="sxs-lookup"><span data-stu-id="d9164-298">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="d9164-299">Speichern Sie die *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="d9164-299">Save the *appsettings.json* file.</span></span> <span data-ttu-id="d9164-300">Aktualisieren Sie den Browser, um festzustellen, ob die Optionswerte aktualisiert wurden:</span><span class="sxs-lookup"><span data-stu-id="d9164-300">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="d9164-301">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="d9164-301">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="d9164-302">Unterstützung für benannte Optionen mit <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> wird als Beispiel 6 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-302">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="d9164-303">Die Unterstützung für benannte Optionen ermöglicht es der Anwendung, zwischen den Konfigurationen benannter Optionen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="d9164-303">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="d9164-304">In der Beispiel-App werden benannte Optionen mit [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) deklariert. Dadurch wird wiederum die Erweiterungsmethode [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="d9164-304">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="d9164-305">Benannte Optionen Unterscheidung nach Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="d9164-305">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="d9164-306">Die Beispielanwendung greift mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> auf die benannten Optionen zu (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-306">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="d9164-307">Bei der Ausführung der Beispielanwendung werden die benannten Optionen zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="d9164-307">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="d9164-308">`named_options_1`-Werte werden von der Konfiguration bereitgestellt. Sie werden aus der *appsettings.json*-Datei geladen.</span><span class="sxs-lookup"><span data-stu-id="d9164-308">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="d9164-309">`named_options_2`-Werte werden bereitgestellt vom:</span><span class="sxs-lookup"><span data-stu-id="d9164-309">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="d9164-310">`named_options_2`-Delegat in `ConfigureServices` für `Option1`.</span><span class="sxs-lookup"><span data-stu-id="d9164-310">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="d9164-311">Der Standardwert für `Option2` wird von der `MyOptions`-Klasse bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d9164-311">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="d9164-312">Konfigurieren aller Optionen mit der ConfigureAll-Methode</span><span class="sxs-lookup"><span data-stu-id="d9164-312">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="d9164-313">Konfigurieren Sie alle Optionsinstanzen mit der <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="d9164-313">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="d9164-314">Der folgende Code konfiguriert `Option1` für alle Konfigurationsinstanzen mit einem gemeinsamen Wert.</span><span class="sxs-lookup"><span data-stu-id="d9164-314">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="d9164-315">Fügen Sie der `Startup.ConfigureServices`-Methode manuell den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="d9164-315">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="d9164-316">Das Ausführen der Beispielanwendung nach dem Hinzufügen des Codes führt zu folgendem Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="d9164-316">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="d9164-317">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="d9164-317">All options are named instances.</span></span> <span data-ttu-id="d9164-318">Vorhandene <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als ob sie auf die `Options.DefaultName`-Instanz abzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="d9164-318">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="d9164-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="d9164-319"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="d9164-320">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d9164-320">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="d9164-321">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention).</span><span class="sxs-lookup"><span data-stu-id="d9164-321">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="d9164-322">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="d9164-322">OptionsBuilder API</span></span>

<span data-ttu-id="d9164-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="d9164-323"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="d9164-324">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="d9164-324">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="d9164-325">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d9164-325">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="d9164-326">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="d9164-326">Use DI services to configure options</span></span>

<span data-ttu-id="d9164-327">Beim Konfigurieren von Optionen haben Sie zwei Möglichkeiten, auf andere Dienste aus Dependency Injections zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="d9164-327">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="d9164-328">Übergeben Sie einen Konfigurationsdelegaten an [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="d9164-328">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="d9164-329">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) stellt Überladungen von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es Ihnen ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="d9164-329">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="d9164-330">Erstellen Ihres eigenen Typs, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und Registrieren des Typs als Dienst.</span><span class="sxs-lookup"><span data-stu-id="d9164-330">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="d9164-331">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="d9164-331">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="d9164-332">Selbst einen eigenen Typ zu erstellen ist gleichbedeutend mit dem, was das Framework für Sie übernimmt, wenn Sie [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) verwenden.</span><span class="sxs-lookup"><span data-stu-id="d9164-332">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="d9164-333">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="d9164-333">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="d9164-334">Überprüfung von Optionen</span><span class="sxs-lookup"><span data-stu-id="d9164-334">Options validation</span></span>

<span data-ttu-id="d9164-335">Sie können Optionen überprüfen, wenn diese konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-335">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="d9164-336">Rufen Sie `Validate` mit einer Überprüfungsmethode auf, die `true` zurückgibt, wenn die Optionen gültig sind, und `false` zurückgibt, wenn sie es nicht sind:</span><span class="sxs-lookup"><span data-stu-id="d9164-336">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

```csharp
// Registration
services.AddOptions<MyOptions>("optionalOptionsName")
    .Configure(o => { }) // Configure the options
    .Validate(o => YourValidationShouldReturnTrueIfValid(o), 
        "custom error");

// Consumption
var monitor = services.BuildServiceProvider()
    .GetService<IOptionsMonitor<MyOptions>>();
  
try
{
    var options = monitor.Get("optionalOptionsName");
}
catch (OptionsValidationException e) 
{
   // e.OptionsName returns "optionalOptionsName"
   // e.OptionsType returns typeof(MyOptions)
   // e.Failures returns a list of errors, which would contain 
   //     "custom error"
}
```

<span data-ttu-id="d9164-337">Im vorherigen Beispiel wurde die benannte Optionsinstanz auf `optionalOptionsName` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d9164-337">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="d9164-338">Die Standardoptionsinstanz ist `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="d9164-338">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="d9164-339">Überprüfungen werden ausgeführt, wenn die Optionsinstanz erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d9164-339">Validation runs when the options instance is created.</span></span> <span data-ttu-id="d9164-340">Eine Optionsinstanz besteht die Überprüfung beim ersten Zugriff garantiert.</span><span class="sxs-lookup"><span data-stu-id="d9164-340">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d9164-341">Die Überprüfung von Optionen schützt nicht vor Änderungen an Optionen nach der Erstellung der Optionsinstanz.</span><span class="sxs-lookup"><span data-stu-id="d9164-341">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="d9164-342">Beispielsweise werden `IOptionsSnapshot`-Optionen einmal pro Anforderung erstellt und überprüft, wenn zum ersten Mal auf die Optionen zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="d9164-342">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="d9164-343">Die `IOptionsSnapshot`-Optionen werden bei nachfolgenden Zugriffsversuchen *für dieselbe Anforderung* nicht erneut überprüft.</span><span class="sxs-lookup"><span data-stu-id="d9164-343">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="d9164-344">Die `Validate`-Methode akzeptiert ein `Func<TOptions, bool>`-Element.</span><span class="sxs-lookup"><span data-stu-id="d9164-344">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="d9164-345">Um die Überprüfung vollständig anzupassen, implementieren Sie `IValidateOptions<TOptions>`. Dies ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d9164-345">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="d9164-346">Eine Überprüfung von mehreren Optionstypen: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="d9164-346">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="d9164-347">Eine Überprüfung, die von einem anderen Optionstyp abhängig ist: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="d9164-347">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="d9164-348">`IValidateOptions` validiert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d9164-348">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="d9164-349">Eine bestimmte benannte Optionsinstanz.</span><span class="sxs-lookup"><span data-stu-id="d9164-349">A specific named options instance.</span></span>
* <span data-ttu-id="d9164-350">Alle Optionen, wenn `name` den Wert `null` aufweist.</span><span class="sxs-lookup"><span data-stu-id="d9164-350">All options when `name` is `null`.</span></span>

<span data-ttu-id="d9164-351">Geben Sie ein `ValidateOptionsResult` aus Ihrer Implementierung der Schnittstelle zurück:</span><span class="sxs-lookup"><span data-stu-id="d9164-351">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="d9164-352">Die Überprüfung auf Basis von Datenanmerkungen ist über das Paket [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) durch Aufrufen der <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*>-Methode in `OptionsBuilder<TOptions>` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d9164-352">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="d9164-353">`Microsoft.Extensions.Options.DataAnnotations` ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="d9164-353">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

```csharp
using Microsoft.Extensions.DependencyInjection;

private class AnnotatedOptions
{
    [Required]
    public string Required { get; set; }

    [StringLength(5, ErrorMessage = "Too long.")]
    public string StringLength { get; set; }

    [Range(-5, 5, ErrorMessage = "Out of range.")]
    public int IntRange { get; set; }
}

[Fact]
public void CanValidateDataAnnotations()
{
    var services = new ServiceCollection();
    services.AddOptions<AnnotatedOptions>()
        .Configure(o =>
        {
            o.StringLength = "111111";
            o.IntRange = 10;
            o.Custom = "nowhere";
        })
        .ValidateDataAnnotations();

    var sp = services.BuildServiceProvider();

    var error = Assert.Throws<OptionsValidationException>(() => 
        sp.GetRequiredService<IOptionsMonitor<AnnotatedOptions>>().CurrentValue);
    ValidateFailure<AnnotatedOptions>(error, Options.DefaultName, 1,
        "DataAnnotation validation failed for members Required " +
            "with the error 'The Required field is required.'.",
        "DataAnnotation validation failed for members StringLength " +
            "with the error 'Too long.'.",
        "DataAnnotation validation failed for members IntRange " +
            "with the error 'Out of range.'.");
}
```

<span data-ttu-id="d9164-354">Die vorzeitige Überprüfung (Fail-fast beim Start) wird für ein späteres Release in Erwägung.</span><span class="sxs-lookup"><span data-stu-id="d9164-354">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="d9164-355">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="d9164-355">Options post-configuration</span></span>

<span data-ttu-id="d9164-356">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="d9164-356">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="d9164-357">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="d9164-357">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d9164-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="d9164-358"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d9164-359">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="d9164-359">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="d9164-360">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="d9164-360">Accessing options during startup</span></span>

<span data-ttu-id="d9164-361"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d9164-361"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="d9164-362">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d9164-362">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d9164-363">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="d9164-363">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="d9164-364">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="d9164-364">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="d9164-365">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="d9164-365">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="d9164-366">Das [Schnittstellentrennungsprinzip oder die Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="d9164-366">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="d9164-367">[Trennung von Zuständigkeiten:](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) Einstellungen für verschiedene App-Komponenten sind nicht voneinander abhängig und nicht aneinander gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="d9164-367">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="d9164-368">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="d9164-368">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="d9164-369">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="d9164-369">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="d9164-370">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d9164-370">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d9164-371">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d9164-371">Prerequisites</span></span>

<span data-ttu-id="d9164-372">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="d9164-372">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="d9164-373">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="d9164-373">Options interfaces</span></span>

<span data-ttu-id="d9164-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="d9164-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="d9164-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="d9164-375"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="d9164-376">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="d9164-376">Change notifications</span></span>
* [<span data-ttu-id="d9164-377">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="d9164-377">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="d9164-378">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d9164-378">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="d9164-379">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="d9164-379">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="d9164-380">Mit [Postkonfigurationsszenarien](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="d9164-380">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="d9164-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="d9164-381"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="d9164-382">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="d9164-382">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="d9164-383">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="d9164-383">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="d9164-384">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="d9164-384">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="d9164-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="d9164-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="d9164-386"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="d9164-386">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="d9164-387">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-387">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="d9164-388">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="d9164-388">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="d9164-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ist nützlich in Szenarien, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="d9164-389"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="d9164-390">Weitere Informationen finden Sie im Abschnitt [Neuladen der Konfigurationsdaten mit IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="d9164-390">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="d9164-391"><xref:Microsoft.Extensions.Options.IOptions%601> kann zum Unterstützen von Optionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-391"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="d9164-392">Allerdings unterstützt <xref:Microsoft.Extensions.Options.IOptions%601> nicht die oben beschriebenen Szenarios von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="d9164-392">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="d9164-393">Sie können <xref:Microsoft.Extensions.Options.IOptions%601> weiterhin in bestehenden Frameworks und Bibliotheken verwenden, die bereits die <xref:Microsoft.Extensions.Options.IOptions%601>-Schnittstelle verwenden und nicht die von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bereitgestellten Szenarios benötigen.</span><span class="sxs-lookup"><span data-stu-id="d9164-393">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="d9164-394">Allgemeine Optionskonfiguration</span><span class="sxs-lookup"><span data-stu-id="d9164-394">General options configuration</span></span>

<span data-ttu-id="d9164-395">Die allgemeine Optionskonfiguration wird als Beispiel 1 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-395">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="d9164-396">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="d9164-396">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="d9164-397">Die folgende Klasse `MyOptions` verfügt über die zwei Eigenschaften: `Option1` und `Option2`.</span><span class="sxs-lookup"><span data-stu-id="d9164-397">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="d9164-398">Das Festlegen von Standardwerten ist optional, aber der Klassenkonstruktor im folgenden Beispiel legt den Standardwert von `Option1` fest.</span><span class="sxs-lookup"><span data-stu-id="d9164-398">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="d9164-399">`Option2` hat den Standardwert festgelegt, indem die Eigenschaft direkt initialisiert wurde (*Models/MyOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-399">`Option2` has a default value set by initializing the property directly (*Models/MyOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="d9164-400">Die `MyOptions`-Klasse wird zum Dienstcontainer mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="d9164-400">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="d9164-401">Das folgende Seitenmodel verwendet [konstruktorbasierte Dependency Injection](xref:mvc/controllers/dependency-injection) mit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, um auf die Einstellungen zugreifen zu können (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-401">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="d9164-402">Die *appsettings.json*-Datei des Beispiels gibt Werte für `option1` und `option2` an:</span><span class="sxs-lookup"><span data-stu-id="d9164-402">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="d9164-403">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="d9164-403">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="d9164-404">Wenn Sie eine benutzerdefinierte <xref:System.Configuration.ConfigurationBuilder>-Klasse verwenden, um Konfigurationsoptionen aus einer Einstellungsdatei zu laden, bestätigen Sie, dass der Basispfad ordnungsgemäß festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="d9164-404">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
>
> ```csharp
> var configBuilder = new ConfigurationBuilder()
>    .SetBasePath(Directory.GetCurrentDirectory())
>    .AddJsonFile("appsettings.json", optional: true);
> var config = configBuilder.Build();
>
> services.Configure<MyOptions>(config);
> ```
>
> <span data-ttu-id="d9164-405">Sie müssen den Basispfad nicht explizit festlegen, wenn Sie Konfigurationsoptionen über <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aus der Einstellungsdatei laden.</span><span class="sxs-lookup"><span data-stu-id="d9164-405">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="d9164-406">Konfigurieren von einfachen Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="d9164-406">Configure simple options with a delegate</span></span>

<span data-ttu-id="d9164-407">Das Konfigurieren von einfachen Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-407">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="d9164-408">Verwenden Sie einen Delegaten zum Festlegen von Optionswerten.</span><span class="sxs-lookup"><span data-stu-id="d9164-408">Use a delegate to set options values.</span></span> <span data-ttu-id="d9164-409">Die Beispielanwendung verwendet die `MyOptionsWithDelegateConfig`-Klasse (*Models/MyOptionsWithDelegateConfig.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-409">The sample app uses the `MyOptionsWithDelegateConfig` class (*Models/MyOptionsWithDelegateConfig.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="d9164-410">Im folgenden Code wird ein zweiter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d9164-410">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d9164-411">Er verwendet einen Delegaten zum Konfigurieren der Bindung mit `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="d9164-411">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="d9164-412">*Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="d9164-412">*Index.cshtml.cs*:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="d9164-413">Sie können mehrere Konfigurationsanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d9164-413">You can add multiple configuration providers.</span></span> <span data-ttu-id="d9164-414">Konfigurationsanbieter sind über NuGet-Pakete verfügbar und werden in der Reihenfolge ihrer Registrierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="d9164-414">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="d9164-415">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="d9164-415">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="d9164-416">Jeder Aufruf von <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> fügt einen <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzu.</span><span class="sxs-lookup"><span data-stu-id="d9164-416">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="d9164-417">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` beide in *appsettings.json* angegeben, aber die Werte von `Option1` und `Option2` werden vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="d9164-417">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json*, but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="d9164-418">Wenn mehr als ein Konfigurationsdienst aktiviert ist, *gewinnt* die letzte angegebene Konfigurationsquelle und legt den Konfigurationswert fest.</span><span class="sxs-lookup"><span data-stu-id="d9164-418">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="d9164-419">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="d9164-419">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="d9164-420">Konfigurieren von Unteroptionen</span><span class="sxs-lookup"><span data-stu-id="d9164-420">Suboptions configuration</span></span>

<span data-ttu-id="d9164-421">Das Konfigurieren von Unteroptionen wird als Beispiel 3 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-421">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="d9164-422">Anwendungen sollten Optionsklassen erstellen, die für bestimmte Szenariogruppen (Klassen) in der Anwendung gelten.</span><span class="sxs-lookup"><span data-stu-id="d9164-422">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="d9164-423">Die Komponenten der Anwendung, die Konfigurationswerte erfordern, sollten nur über Zugriff auf die Konfigurationswerte verfügen, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="d9164-423">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="d9164-424">Beim Binden von Optionen zur Konfiguration ist jede Eigenschaft im Optionstyp an einen Konfigurationsschlüssel des `property[:sub-property:]`-Formulars gebunden.</span><span class="sxs-lookup"><span data-stu-id="d9164-424">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="d9164-425">Die `MyOptions.Option1`-Eigenschaft ist beispielsweise an den Schlüssel `Option1` gebunden, der von der `option1`-Eigenschaft in *appsettings.json* gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="d9164-425">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="d9164-426">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d9164-426">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="d9164-427">Er verbindet `MySubOptions` mit dem `subsection`-Abschnitt der *appSettings.json*-Datei:</span><span class="sxs-lookup"><span data-stu-id="d9164-427">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="d9164-428">Die `GetSection`-Methode erfordert den Namespace <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="d9164-428">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="d9164-429">Die *appsettings.json*-Datei des Beispiels definiert ein `subsection`-Element mit Schlüsseln für `suboption1` und `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="d9164-429">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="d9164-430">Die `MySubOptions`-Klasse definiert die Eigenschaften `SubOption1` und `SubOption2` zur Aufnahme der Optionswerte (*Models/MySubOptions.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-430">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values (*Models/MySubOptions.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="d9164-431">Die `OnGet`-Methode des Seitenmodells gibt eine Zeichenfolge mit den Optionswerten (*Pages/Index.cshtml.cs*) zurück:</span><span class="sxs-lookup"><span data-stu-id="d9164-431">The page model's `OnGet` method returns a string with the options values (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="d9164-432">Wenn die App ausgeführt wird, gibt die `OnGet`-Methode eine Zeichenfolge zurück, die die Werte der untergeordneten Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="d9164-432">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="d9164-433">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="d9164-433">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="d9164-434">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden, werden im Beispiel 4 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-434">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="d9164-435">Optionen können in einem Ansichtsmodell oder durch direkte Injection von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in eine Ansicht (*Pages/Index.cshtml.cs*) angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="d9164-435">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="d9164-436">Die Beispiel-App zeigt das Einfügen von `IOptionsMonitor<MyOptions>` mit einer `@inject`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="d9164-436">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="d9164-437">Wenn die App ausgeführt wird, werden die Optionswerte in der gerenderten Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d9164-437">When the app is run, the options values are shown in the rendered page:</span></span>

![Optionswerte Option1: value1_from_json und Option2: -1 werden aus dem Modell geladen und in die Ansicht eingefügt.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="d9164-439">Neuladen der Konfigurationsdaten mit IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="d9164-439">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="d9164-440">Das Neuladen der Konfigurationsdaten mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> wird im Beispiel 5 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-440">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="d9164-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> unterstützt das Neuladen von Optionen mit minimalem Verarbeitungsaufwand.</span><span class="sxs-lookup"><span data-stu-id="d9164-441"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="d9164-442">Optionen werden einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="d9164-442">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="d9164-443">Im folgenden Beispiel wird veranschaulicht, wie eine neue <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nach den *appsettings.json*-Veränderungen erstellt wird (*Pages/Index.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="d9164-443">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes (*Pages/Index.cshtml.cs*).</span></span> <span data-ttu-id="d9164-444">Mehrere Anforderungen an den Server geben konstante Werte zurück, die von der *appsettings.json*-Datei bereitgestellt werden, bis die Datei geändert wird und die Konfiguration neu lädt.</span><span class="sxs-lookup"><span data-stu-id="d9164-444">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="d9164-445">Die folgende Abbildung zeigt die anfänglichen `option1`- und `option2`-Werte, die aus der *appsettings.json*-Datei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="d9164-445">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="d9164-446">Ändern Sie die Werte in der *appsettings.json*-Datei auf `value1_from_json UPDATED` und `200`.</span><span class="sxs-lookup"><span data-stu-id="d9164-446">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="d9164-447">Speichern Sie die *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="d9164-447">Save the *appsettings.json* file.</span></span> <span data-ttu-id="d9164-448">Aktualisieren Sie den Browser, um festzustellen, ob die Optionswerte aktualisiert wurden:</span><span class="sxs-lookup"><span data-stu-id="d9164-448">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="d9164-449">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="d9164-449">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="d9164-450">Unterstützung für benannte Optionen mit <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> wird als Beispiel 6 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d9164-450">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="d9164-451">Die Unterstützung für benannte Optionen ermöglicht es der Anwendung, zwischen den Konfigurationen benannter Optionen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="d9164-451">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="d9164-452">In der Beispiel-App werden benannte Optionen mit [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) deklariert. Dadurch wird wiederum die Erweiterungsmethode [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="d9164-452">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="d9164-453">Benannte Optionen Unterscheidung nach Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="d9164-453">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="d9164-454">Die Beispielanwendung greift mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> auf die benannten Optionen zu (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="d9164-454">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="d9164-455">Bei der Ausführung der Beispielanwendung werden die benannten Optionen zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="d9164-455">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="d9164-456">`named_options_1`-Werte werden von der Konfiguration bereitgestellt. Sie werden aus der *appsettings.json*-Datei geladen.</span><span class="sxs-lookup"><span data-stu-id="d9164-456">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="d9164-457">`named_options_2`-Werte werden bereitgestellt vom:</span><span class="sxs-lookup"><span data-stu-id="d9164-457">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="d9164-458">`named_options_2`-Delegat in `ConfigureServices` für `Option1`.</span><span class="sxs-lookup"><span data-stu-id="d9164-458">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="d9164-459">Der Standardwert für `Option2` wird von der `MyOptions`-Klasse bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d9164-459">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="d9164-460">Konfigurieren aller Optionen mit der ConfigureAll-Methode</span><span class="sxs-lookup"><span data-stu-id="d9164-460">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="d9164-461">Konfigurieren Sie alle Optionsinstanzen mit der <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="d9164-461">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="d9164-462">Der folgende Code konfiguriert `Option1` für alle Konfigurationsinstanzen mit einem gemeinsamen Wert.</span><span class="sxs-lookup"><span data-stu-id="d9164-462">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="d9164-463">Fügen Sie der `Startup.ConfigureServices`-Methode manuell den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="d9164-463">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="d9164-464">Das Ausführen der Beispielanwendung nach dem Hinzufügen des Codes führt zu folgendem Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="d9164-464">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="d9164-465">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="d9164-465">All options are named instances.</span></span> <span data-ttu-id="d9164-466">Vorhandene <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als ob sie auf die `Options.DefaultName`-Instanz abzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="d9164-466">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="d9164-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="d9164-467"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="d9164-468">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d9164-468">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="d9164-469">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention).</span><span class="sxs-lookup"><span data-stu-id="d9164-469">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="d9164-470">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="d9164-470">OptionsBuilder API</span></span>

<span data-ttu-id="d9164-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="d9164-471"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="d9164-472">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="d9164-472">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="d9164-473">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d9164-473">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="d9164-474">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="d9164-474">Use DI services to configure options</span></span>

<span data-ttu-id="d9164-475">Beim Konfigurieren von Optionen haben Sie zwei Möglichkeiten, auf andere Dienste aus Dependency Injections zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="d9164-475">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="d9164-476">Übergeben Sie einen Konfigurationsdelegaten an [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="d9164-476">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="d9164-477">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) stellt Überladungen von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es Ihnen ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="d9164-477">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="d9164-478">Erstellen Ihres eigenen Typs, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und Registrieren des Typs als Dienst.</span><span class="sxs-lookup"><span data-stu-id="d9164-478">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="d9164-479">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="d9164-479">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="d9164-480">Selbst einen eigenen Typ zu erstellen ist gleichbedeutend mit dem, was das Framework für Sie übernimmt, wenn Sie [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) verwenden.</span><span class="sxs-lookup"><span data-stu-id="d9164-480">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="d9164-481">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="d9164-481">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="d9164-482">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="d9164-482">Options post-configuration</span></span>

<span data-ttu-id="d9164-483">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="d9164-483">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="d9164-484">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="d9164-484">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d9164-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="d9164-485"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="d9164-486">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="d9164-486">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="d9164-487">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="d9164-487">Accessing options during startup</span></span>

<span data-ttu-id="d9164-488"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d9164-488"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="d9164-489">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="d9164-489">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d9164-490">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="d9164-490">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d9164-491">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d9164-491">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
