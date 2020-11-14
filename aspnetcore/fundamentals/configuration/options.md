---
title: Optionsmuster in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Optionsmuster verwenden, um Gruppen von zusammengehörigen Einstellungen in ASP.NET Core-Anwendungen darzustellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/20/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/configuration/options
ms.openlocfilehash: dedc17d7d793a6fd2eac1c8017b704d98a86f1cb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061092"
---
# <a name="options-pattern-in-aspnet-core"></a><span data-ttu-id="3d8b5-103">Optionsmuster in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3d8b5-103">Options pattern in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3d8b5-104">Von [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3d8b5-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Rick Anderson](https://twitter.com/RickAndMSFT).</span></span>

<span data-ttu-id="3d8b5-105">Das Optionsmuster verwendet Klassen, um stark typisierten Zugriff auf zusammengehörige Einstellungsgruppen zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-105">The options pattern uses classes to provide strongly typed access to groups of related settings.</span></span> <span data-ttu-id="3d8b5-106">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-106">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="3d8b5-107">Das [Schnittstellentrennungsprinzip oder die Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-107">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="3d8b5-108">[Trennung von Zuständigkeiten:](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) Einstellungen für verschiedene App-Komponenten sind nicht voneinander abhängig und nicht aneinander gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-108">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="3d8b5-109">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-109">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="3d8b5-110">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-110">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="3d8b5-111">Dieses Thema enthält Informationen zu den Optionsmustern in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-111">This topic provides information on the options pattern in ASP.NET Core.</span></span> <span data-ttu-id="3d8b5-112">Informationen zur Verwendung des Optionsmusters in Konsolen-Apps finden Sie unter [Optionsmuster in .NET](/dotnet/core/extensions/options).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-112">For information on using the options pattern in console apps, see [Options pattern in .NET](/dotnet/core/extensions/options).</span></span>

<span data-ttu-id="3d8b5-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3d8b5-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="optpat"></a>

## <a name="bind-hierarchical-configuration"></a><span data-ttu-id="3d8b5-114">Binden von hierarchischen Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="3d8b5-114">Bind hierarchical configuration</span></span>

[!INCLUDE[](~/includes/bind.md)]

<a name="oi"></a>

## <a name="options-interfaces"></a><span data-ttu-id="3d8b5-115">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-115">Options interfaces</span></span>

<span data-ttu-id="3d8b5-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-116"><xref:Microsoft.Extensions.Options.IOptions%601>:</span></span>

* <span data-ttu-id="3d8b5-117">Folgendes wird **nicht** unterstützt: Lesen von Konfigurationsdaten, nachdem die App gestartet wurde</span><span class="sxs-lookup"><span data-stu-id="3d8b5-117">Does \* **not** _ support: _ Reading of configuration data after the app has started.</span></span>
  * [<span data-ttu-id="3d8b5-118">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-118">Named options</span></span>](#named)
* <span data-ttu-id="3d8b5-119">Ist als [Singleton](xref:fundamentals/dependency-injection#singleton) registriert und kann in eine beliebige [Dienstlebensdauer](xref:fundamentals/dependency-injection#service-lifetimes) eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="3d8b5-119">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="3d8b5-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-120"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>:</span></span>

* <span data-ttu-id="3d8b5-121">Ist in Szenarios nützlich, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-121">Is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="3d8b5-122">Weitere Informationen finden Sie unter [Verwenden von IOptionsSnapshot zum Lesen aktualisierter Daten](#ios).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-122">For more information, see [Use IOptionsSnapshot to read updated data](#ios).</span></span>
* <span data-ttu-id="3d8b5-123">Ist als [Bereichsbezogen](xref:fundamentals/dependency-injection#scoped) registriert und kann deshalb nicht in einen Singleton-Dienst eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-123">Is registered as [Scoped](xref:fundamentals/dependency-injection#scoped) and therefore cannot be injected into a Singleton service.</span></span>
* <span data-ttu-id="3d8b5-124">Unterstützt [benannten Optionen](#named)</span><span class="sxs-lookup"><span data-stu-id="3d8b5-124">Supports [named options](#named)</span></span>

<span data-ttu-id="3d8b5-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-125"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

* <span data-ttu-id="3d8b5-126">Wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten</span><span class="sxs-lookup"><span data-stu-id="3d8b5-126">Is used to retrieve options and manage options notifications for `TOptions` instances.</span></span>
* <span data-ttu-id="3d8b5-127">Ist als [Singleton](xref:fundamentals/dependency-injection#singleton) registriert und kann in eine beliebige [Dienstlebensdauer](xref:fundamentals/dependency-injection#service-lifetimes) eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="3d8b5-127">Is registered as a [Singleton](xref:fundamentals/dependency-injection#singleton) and can be injected into any [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>
* <span data-ttu-id="3d8b5-128">Unterstützt:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-128">Supports:</span></span>
  * <span data-ttu-id="3d8b5-129">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-129">Change notifications</span></span>
  * [<span data-ttu-id="3d8b5-130">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-130">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
  * [<span data-ttu-id="3d8b5-131">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="3d8b5-131">Reloadable configuration</span></span>](#ios)
  * <span data-ttu-id="3d8b5-132">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="3d8b5-132">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>
  
<span data-ttu-id="3d8b5-133">Mit [Postkonfigurationsszenarios](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-133">[Post-configuration](#options-post-configuration) scenarios enable setting or changing options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="3d8b5-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-134"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="3d8b5-135">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-135">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="3d8b5-136">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-136">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="3d8b5-137">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-137">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="3d8b5-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-138"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="3d8b5-139"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-139">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="3d8b5-140">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-140">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="3d8b5-141">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-141">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<a name="ios"></a>

## <a name="use-ioptionssnapshot-to-read-updated-data"></a><span data-ttu-id="3d8b5-142">Verwenden von IOptionsSnapshot zum Lesen aktualisierter Daten</span><span class="sxs-lookup"><span data-stu-id="3d8b5-142">Use IOptionsSnapshot to read updated data</span></span>

<span data-ttu-id="3d8b5-143">Mithilfe von <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> werden Optionen einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-143">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span> <span data-ttu-id="3d8b5-144">Änderungen an der Konfiguration werden gelesen, nachdem die App gestartet wurde, wenn Konfigurationsanbieter verwendet werden, die das Lesen aktualisierter Konfigurationswerte unterstützen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-144">Changes to the configuration are read after the app starts when using configuration providers that support reading updated configuration values.</span></span>

<span data-ttu-id="3d8b5-145">Der Unterschied zwischen `IOptionsMonitor` und `IOptionsSnapshot` ist folgender:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-145">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="3d8b5-146">`IOptionsMonitor` ist ein [Singleton-Dienst](xref:fundamentals/dependency-injection#singleton), der zu jeder Zeit aktuelle Optionswerte empfängt, was insbesondere bei Singleton-Abhängigkeiten nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-146">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="3d8b5-147">`IOptionsSnapshot` ist ein [bereichsbezogener Dienst](xref:fundamentals/dependency-injection#scoped) und bietet eine Momentaufnahme der Optionen zu dem Zeitpunkt, da das `IOptionsSnapshot<T>`-Objekt konstruiert wird.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-147">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="3d8b5-148">Momentaufnahmen von Optionen sind für die Verwendung mit vorübergehenden und bereichsbezogenen Abhängigkeiten bestimmt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-148">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="3d8b5-149">Der folgende Code verwendet <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-149">The following code uses <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>.</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestSnap.cshtml.cs?name=snippet)]

<span data-ttu-id="3d8b5-150">Der folgende Code registriert eine Konfigurationsinstanz, mit der `MyOptions` eine Bindung herstellt:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-150">The following code registers a configuration instance which `MyOptions` binds against:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="3d8b5-151">Im vorangehenden Code werden Änderungen an der JSON-Konfigurationsdatei nach dem Start der App gelesen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-151">In the preceding code, changes to the JSON configuration file after the app has started are read.</span></span>

## <a name="ioptionsmonitor"></a><span data-ttu-id="3d8b5-152">IOptionsMonitor</span><span class="sxs-lookup"><span data-stu-id="3d8b5-152">IOptionsMonitor</span></span>

<span data-ttu-id="3d8b5-153">Der folgende Code registriert eine Konfigurationsinstanz, mit der `MyOptions` eine Bindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-153">The following code registers a configuration instance which `MyOptions` binds against.</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="3d8b5-154">Im folgenden Beispiel wird <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> verwendet:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-154">The following example uses <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestMonitor.cshtml.cs?name=snippet)]

<span data-ttu-id="3d8b5-155">Im vorangehenden Code werden standardmäßig Änderungen an der JSON-Konfigurationsdatei gelesen, nachdem die App gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-155">In the preceding code, by default, changes to the JSON configuration file after the app has started are read.</span></span>

<a name="named"></a>

## <a name="named-options-support-using-iconfigurenamedoptions"></a><span data-ttu-id="3d8b5-156">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="3d8b5-156">Named options support using IConfigureNamedOptions</span></span>

<span data-ttu-id="3d8b5-157">Benannte Optionen...</span><span class="sxs-lookup"><span data-stu-id="3d8b5-157">Named options:</span></span>

* <span data-ttu-id="3d8b5-158">sind nützlich, wenn mehrere Konfigurationsabschnitte an die gleichen Eigenschaften gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-158">Are useful when multiple configuration sections bind to the same properties.</span></span>
* <span data-ttu-id="3d8b5-159">unterscheiden zwischen Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-159">Are case sensitive.</span></span>

<span data-ttu-id="3d8b5-160">Betrachten Sie die folgende *appsettings.json* -Datei:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-160">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/appsettings.NO.json)]

<span data-ttu-id="3d8b5-161">Anstatt zwei Klassen für die Bindung von `TopItem:Month` und `TopItem:Year` zu erstellen, wird folgende Klasse für jeden Abschnitt verwendet:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-161">Rather than creating two classes to bind `TopItem:Month` and `TopItem:Year`, the following class is used for each section:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Models/TopItemSettings.cs)]

<span data-ttu-id="3d8b5-162">Der folgende Code dient zum Konfigurieren der benannten Optionen:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-162">The following code configures the named options:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/StartupNO.cs?name=snippet_Example2)]

<span data-ttu-id="3d8b5-163">Der folgende Code zeigt die benannten Optionen an:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-163">The following code displays the named options:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/TestNO.cshtml.cs?name=snippet)]

<span data-ttu-id="3d8b5-164">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-164">All options are named instances.</span></span> <span data-ttu-id="3d8b5-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als würden sie die `Options.DefaultName`-Instanz anzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-165"><xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="3d8b5-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-166"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="3d8b5-167">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-167">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="3d8b5-168">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-168">The `null` named option is used to target all of the named instances instead of a specific named instance.</span></span> <span data-ttu-id="3d8b5-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-169"><xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention.</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="3d8b5-170">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="3d8b5-170">OptionsBuilder API</span></span>

<span data-ttu-id="3d8b5-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-171"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="3d8b5-172">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-172">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="3d8b5-173">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-173">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

<span data-ttu-id="3d8b5-174">`OptionsBuilder` wird im Abschnitt [Überprüfung von Optionen](#val) verwendet.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-174">`OptionsBuilder` is used in the [Options validation](#val) section.</span></span>

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="3d8b5-175">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-175">Use DI services to configure options</span></span>

<span data-ttu-id="3d8b5-176">Der Zugriff auf Dienste ist über die Dependency Injection möglich, während Optionen auf zwei Arten konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-176">Services can be accessed from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="3d8b5-177">Übergeben Sie einen Konfigurationsdelegaten an [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-177">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="3d8b5-178">`OptionsBuilder<TOptions>` stellt Überladungen von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-178">`OptionsBuilder<TOptions>` provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow use of up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="3d8b5-179">Erstellen Sie einen Typ, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und registrieren Sie den Typ als Dienst.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-179">Create a type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="3d8b5-180">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-180">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="3d8b5-181">Das Erstellen eines Typs entspricht den Aktionen des Frameworks beim Aufruf von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-181">Creating a type is equivalent to what the framework does when calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="3d8b5-182">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-182">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

<a name="val"></a>

## <a name="options-validation"></a><span data-ttu-id="3d8b5-183">Überprüfung von Optionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-183">Options validation</span></span>

<span data-ttu-id="3d8b5-184">Bei der Überprüfung der Optionen können Optionswerte überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-184">Options validation enables option values to be validated.</span></span>

<span data-ttu-id="3d8b5-185">Betrachten Sie die folgende *appsettings.json* -Datei:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-185">Consider the following *appsettings.json* file:</span></span>

[!code-json[](~/fundamentals/configuration/options/samples/3.x/OptionsValidationSample/appsettings.Dev2.json)]

<span data-ttu-id="3d8b5-186">Die folgende Klasse erstellt eine Bindung zum `"MyConfig"`-Konfigurationsabschnitt und wendet einige `DataAnnotations`-Regeln an:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-186">The following class binds to the `"MyConfig"` configuration section and applies a couple of `DataAnnotations` rules:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigOptions.cs?name=snippet)]

<span data-ttu-id="3d8b5-187">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="3d8b5-187">The following code:</span></span>

* <span data-ttu-id="3d8b5-188">Mit dem Code wird die <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A>-Methode abgerufen, um eine [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1)-Klasse zu erhalten, die an die `MyConfigOptions`-Klasse gebunden wird.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-188">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions%2A> to get an [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) that binds to the `MyConfigOptions` class.</span></span>
* <span data-ttu-id="3d8b5-189">Mit dem Code wird <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> abgerufen, um die Validierung mithilfe von `DataAnnotations` zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-189">Calls <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations%2A> to enable validation using `DataAnnotations`.</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup.cs?name=snippet)]

<span data-ttu-id="3d8b5-190">Die `ValidateDataAnnotations`-Erweiterungsmethode wird im NuGet-Paket [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) definiert.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-190">The `ValidateDataAnnotations` extension method is defined in the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) NuGet package.</span></span> <span data-ttu-id="3d8b5-191">Für Web-Apps, die das `Microsoft.NET.Sdk.Web` SDK verwenden, wird auf dieses Paket implizit über das geteilte Framework verwiesen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-191">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, this package is referenced implicitly from the shared framework.</span></span>

<span data-ttu-id="3d8b5-192">Der folgende Code zeigt die Konfigurationswerte oder die Überprüfungsfehler an:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-192">The following code displays the configuration values or the validation errors:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="3d8b5-193">Der folgende Code wendet mithilfe eines Delegaten eine komplexere Überprüfungsregel an:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-193">The following code applies a more complex validation rule using a delegate:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Startup2.cs?name=snippet)]

### <a name="ivalidateoptions-for-complex-validation"></a><span data-ttu-id="3d8b5-194">IValidateOptions für die komplexe Überprüfung</span><span class="sxs-lookup"><span data-stu-id="3d8b5-194">IValidateOptions for complex validation</span></span>

<span data-ttu-id="3d8b5-195">Die folgende Klasse implementiert <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-195">The following class implements <xref:Microsoft.Extensions.Options.IValidateOptions`1>:</span></span>

[!code-csharp[](options/samples/3.x/OptionsValidationSample/Configuration/MyConfigValidation.cs?name=snippet)]

<span data-ttu-id="3d8b5-196">`IValidateOptions` ermöglicht das Verschieben des Überprüfungscodes aus `StartUp` und in eine Klasse.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-196">`IValidateOptions` enables moving the validation code out of `StartUp` and into a class.</span></span>

<span data-ttu-id="3d8b5-197">Mit dem vorangehenden Code wird die Überprüfung in `Startup.ConfigureServices` aktiviert:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-197">Using the preceding code, validation is enabled in `Startup.ConfigureServices` with the following code:</span></span>

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

## <a name="options-post-configuration"></a><span data-ttu-id="3d8b5-198">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="3d8b5-198">Options post-configuration</span></span>

<span data-ttu-id="3d8b5-199">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-199">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="3d8b5-200">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-200">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3d8b5-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-201"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3d8b5-202">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-202">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="3d8b5-203">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="3d8b5-203">Accessing options during startup</span></span>

<span data-ttu-id="3d8b5-204"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-204"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, 
    IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="3d8b5-205">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-205">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3d8b5-206">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-206">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

## <a name="optionsconfigurationextensions-nuget-package"></a><span data-ttu-id="3d8b5-207">NuGet-Paket Options.ConfigurationExtensions</span><span class="sxs-lookup"><span data-stu-id="3d8b5-207">Options.ConfigurationExtensions NuGet package</span></span>

<span data-ttu-id="3d8b5-208">Auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) wird implizit in ASP.NET Core-Apps verwiesen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-208">The [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package is implicitly referenced in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="3d8b5-209">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-209">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="3d8b5-210">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-210">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="3d8b5-211">Das [Schnittstellentrennungsprinzip oder die Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-211">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="3d8b5-212">[Trennung von Zuständigkeiten:](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) Einstellungen für verschiedene App-Komponenten sind nicht voneinander abhängig und nicht aneinander gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-212">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="3d8b5-213">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-213">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="3d8b5-214">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-214">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="3d8b5-215">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3d8b5-215">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3d8b5-216">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-216">Prerequisites</span></span>

<span data-ttu-id="3d8b5-217">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-217">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="3d8b5-218">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-218">Options interfaces</span></span>

<span data-ttu-id="3d8b5-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-219"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="3d8b5-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-220"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="3d8b5-221">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-221">Change notifications</span></span>
* [<span data-ttu-id="3d8b5-222">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-222">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="3d8b5-223">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="3d8b5-223">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="3d8b5-224">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="3d8b5-224">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="3d8b5-225">Mit [Postkonfigurationsszenarien](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-225">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="3d8b5-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-226"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="3d8b5-227">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-227">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="3d8b5-228">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-228">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="3d8b5-229">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-229">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="3d8b5-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-230"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="3d8b5-231"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-231">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="3d8b5-232">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-232">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="3d8b5-233">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-233">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="3d8b5-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ist nützlich in Szenarien, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-234"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="3d8b5-235">Weitere Informationen finden Sie im Abschnitt [Neuladen der Konfigurationsdaten mit IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-235">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="3d8b5-236"><xref:Microsoft.Extensions.Options.IOptions%601> kann zum Unterstützen von Optionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-236"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="3d8b5-237">Allerdings unterstützt <xref:Microsoft.Extensions.Options.IOptions%601> nicht die oben beschriebenen Szenarios von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-237">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="3d8b5-238">Sie können <xref:Microsoft.Extensions.Options.IOptions%601> weiterhin in bestehenden Frameworks und Bibliotheken verwenden, die bereits die <xref:Microsoft.Extensions.Options.IOptions%601>-Schnittstelle verwenden und nicht die von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bereitgestellten Szenarios benötigen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-238">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="3d8b5-239">Allgemeine Optionskonfiguration</span><span class="sxs-lookup"><span data-stu-id="3d8b5-239">General options configuration</span></span>

<span data-ttu-id="3d8b5-240">Die allgemeine Optionskonfiguration wird als Beispiel 1 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-240">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="3d8b5-241">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-241">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="3d8b5-242">Die folgende Klasse `MyOptions` verfügt über die zwei Eigenschaften: `Option1` und `Option2`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-242">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="3d8b5-243">Das Festlegen von Standardwerten ist optional, aber der Klassenkonstruktor im folgenden Beispiel legt den Standardwert von `Option1` fest.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-243">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="3d8b5-244">`Option2` hat den Standardwert festgelegt, indem die Eigenschaft direkt initialisiert wurde ( *Models/MyOptions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-244">`Option2` has a default value set by initializing the property directly ( *Models/MyOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="3d8b5-245">Die `MyOptions`-Klasse wird zum Dienstcontainer mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-245">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="3d8b5-246">Das folgende Seitenmodel verwendet [konstruktorbasierte Dependency Injection](xref:mvc/controllers/dependency-injection) mit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, um auf die Einstellungen zugreifen zu können ( *Pages/Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-246">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="3d8b5-247">Die *appsettings.json* -Datei des Beispiels gibt Werte für `option1` und `option2` an:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-247">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="3d8b5-248">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-248">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="3d8b5-249">Wenn Sie eine benutzerdefinierte <xref:System.Configuration.ConfigurationBuilder>-Klasse verwenden, um Konfigurationsoptionen aus einer Einstellungsdatei zu laden, bestätigen Sie, dass der Basispfad ordnungsgemäß festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-249">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="3d8b5-250">Sie müssen den Basispfad nicht explizit festlegen, wenn Sie Konfigurationsoptionen über <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aus der Einstellungsdatei laden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-250">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="3d8b5-251">Konfigurieren von einfachen Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="3d8b5-251">Configure simple options with a delegate</span></span>

<span data-ttu-id="3d8b5-252">Das Konfigurieren von einfachen Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-252">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="3d8b5-253">Verwenden Sie einen Delegaten zum Festlegen von Optionswerten.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-253">Use a delegate to set options values.</span></span> <span data-ttu-id="3d8b5-254">Die Beispielanwendung verwendet die `MyOptionsWithDelegateConfig`-Klasse ( *Models/MyOptionsWithDelegateConfig.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-254">The sample app uses the `MyOptionsWithDelegateConfig` class ( *Models/MyOptionsWithDelegateConfig.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="3d8b5-255">Im folgenden Code wird ein zweiter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-255">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3d8b5-256">Er verwendet einen Delegaten zum Konfigurieren der Bindung mit `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-256">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="3d8b5-257">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="3d8b5-257">*Index.cshtml.cs* :</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="3d8b5-258">Sie können mehrere Konfigurationsanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-258">You can add multiple configuration providers.</span></span> <span data-ttu-id="3d8b5-259">Konfigurationsanbieter sind über NuGet-Pakete verfügbar und werden in der Reihenfolge ihrer Registrierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-259">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="3d8b5-260">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-260">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="3d8b5-261">Jeder Aufruf von <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> fügt einen <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzu.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-261">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="3d8b5-262">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` beide in *appsettings.json* angegeben, aber die Werte von `Option1` und `Option2` werden vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-262">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* , but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="3d8b5-263">Wenn mehr als ein Konfigurationsdienst aktiviert ist, *gewinnt* die letzte angegebene Konfigurationsquelle und legt den Konfigurationswert fest.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-263">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="3d8b5-264">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-264">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="3d8b5-265">Konfigurieren von Unteroptionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-265">Suboptions configuration</span></span>

<span data-ttu-id="3d8b5-266">Das Konfigurieren von Unteroptionen wird als Beispiel 3 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-266">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="3d8b5-267">Anwendungen sollten Optionsklassen erstellen, die für bestimmte Szenariogruppen (Klassen) in der Anwendung gelten.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-267">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="3d8b5-268">Die Komponenten der Anwendung, die Konfigurationswerte erfordern, sollten nur über Zugriff auf die Konfigurationswerte verfügen, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-268">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="3d8b5-269">Beim Binden von Optionen zur Konfiguration ist jede Eigenschaft im Optionstyp an einen Konfigurationsschlüssel des `property[:sub-property:]`-Formulars gebunden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-269">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="3d8b5-270">Die `MyOptions.Option1`-Eigenschaft ist beispielsweise an den Schlüssel `Option1` gebunden, der von der `option1`-Eigenschaft in *appsettings.json* gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-270">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="3d8b5-271">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-271">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3d8b5-272">Dadurch wird `MySubOptions` an den Abschnitt `subsection` der *appsettings.json* -Datei gebunden:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-272">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="3d8b5-273">Die `GetSection`-Methode erfordert den Namespace <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-273">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="3d8b5-274">Die *appsettings.json* -Datei des Beispiels definiert einen `subsection`-Member mit Schlüsseln für `suboption1` und `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-274">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="3d8b5-275">Die `MySubOptions`-Klasse definiert die Eigenschaften `SubOption1` und `SubOption2` zur Aufnahme der Optionswerte ( *Models/MySubOptions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-275">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values ( *Models/MySubOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="3d8b5-276">Die `OnGet`-Methode des Seitenmodells gibt eine Zeichenfolge mit den Optionswerten ( *Pages/Index.cshtml.cs* ) zurück:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-276">The page model's `OnGet` method returns a string with the options values ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="3d8b5-277">Wenn die App ausgeführt wird, gibt die `OnGet`-Methode eine Zeichenfolge zurück, die die Werte der untergeordneten Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-277">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-injection"></a><span data-ttu-id="3d8b5-278">Einfügung von Optionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-278">Options injection</span></span>

<span data-ttu-id="3d8b5-279">Die Einfügung von Optionen ist als Beispiel 4 in der Beispiel-App dargestellt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-279">Options injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="3d8b5-280">Fügen Sie <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> ein in:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-280">Inject <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into:</span></span>

* <span data-ttu-id="3d8b5-281">Eine Razor-Seite oder MVC-Ansicht mithilfe der [`@inject`](xref:mvc/views/razor#inject) Razor-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-281">A Razor page or MVC view with the [`@inject`](xref:mvc/views/razor#inject) Razor directive.</span></span>
* <span data-ttu-id="3d8b5-282">Ein Seiten- oder Ansichtsmodell.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-282">A page or view model.</span></span>

<span data-ttu-id="3d8b5-283">Im folgenden Beispiel aus der Beispiel-App wird <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in ein Seitenmodell eingefügt ( *Pages/Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-283">The following example from the sample app injects <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> into a page model ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="3d8b5-284">Die Beispiel-App zeigt das Einfügen von `IOptionsMonitor<MyOptions>` mit einer `@inject`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-284">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="3d8b5-285">Wenn die App ausgeführt wird, werden die Optionswerte in der gerenderten Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-285">When the app is run, the options values are shown in the rendered page:</span></span>

![Optionswerte Option1: value1_from_json und Option2: -1 werden aus dem Modell geladen und in die Ansicht eingefügt.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="3d8b5-287">Neuladen der Konfigurationsdaten mit IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="3d8b5-287">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="3d8b5-288">Das Neuladen der Konfigurationsdaten mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> wird im Beispiel 5 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-288">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="3d8b5-289">Mithilfe von <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> werden Optionen einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-289">Using <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601>, options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="3d8b5-290">Der Unterschied zwischen `IOptionsMonitor` und `IOptionsSnapshot` ist folgender:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-290">The difference between `IOptionsMonitor` and `IOptionsSnapshot` is that:</span></span>

* <span data-ttu-id="3d8b5-291">`IOptionsMonitor` ist ein [Singleton-Dienst](xref:fundamentals/dependency-injection#singleton), der zu jeder Zeit aktuelle Optionswerte empfängt, was insbesondere bei Singleton-Abhängigkeiten nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-291">`IOptionsMonitor` is a [singleton service](xref:fundamentals/dependency-injection#singleton) that retrieves current option values at any time, which is especially useful in singleton dependencies.</span></span>
* <span data-ttu-id="3d8b5-292">`IOptionsSnapshot` ist ein [bereichsbezogener Dienst](xref:fundamentals/dependency-injection#scoped) und bietet eine Momentaufnahme der Optionen zu dem Zeitpunkt, da das `IOptionsSnapshot<T>`-Objekt konstruiert wird.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-292">`IOptionsSnapshot` is a [scoped service](xref:fundamentals/dependency-injection#scoped) and provides a snapshot of the options at the time the `IOptionsSnapshot<T>` object is constructed.</span></span> <span data-ttu-id="3d8b5-293">Momentaufnahmen von Optionen sind für die Verwendung mit vorübergehenden und bereichsbezogenen Abhängigkeiten bestimmt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-293">Options snapshots are designed for use with transient and scoped dependencies.</span></span>

<span data-ttu-id="3d8b5-294">Im folgenden Beispiel wird veranschaulicht, wie eine neue <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nach den *appsettings.json* -Änderungen erstellt wird ( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-294">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes ( *Pages/Index.cshtml.cs* ).</span></span> <span data-ttu-id="3d8b5-295">Mehrere Anforderungen an den Server geben konstante Werte zurück, die von der *appsettings.json* -Datei bereitgestellt werden, bis die Datei geändert wird und die Konfiguration neu lädt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-295">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="3d8b5-296">Die folgende Abbildung zeigt die anfänglichen `option1`- und `option2`-Werte, die aus der *appsettings.json* -Datei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-296">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="3d8b5-297">Ändern Sie die Werte in der *appsettings.json* -Datei in `value1_from_json UPDATED` und `200`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-297">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="3d8b5-298">Speichern Sie die *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-298">Save the *appsettings.json* file.</span></span> <span data-ttu-id="3d8b5-299">Aktualisieren Sie den Browser, um festzustellen, ob die Optionswerte aktualisiert wurden:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-299">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="3d8b5-300">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="3d8b5-300">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="3d8b5-301">Unterstützung für benannte Optionen mit <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> wird als Beispiel 6 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-301">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="3d8b5-302">Die Unterstützung für benannte Optionen ermöglicht es der Anwendung, zwischen den Konfigurationen benannter Optionen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-302">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="3d8b5-303">In der Beispiel-App werden benannte Optionen mit [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) deklariert. Dadurch wird wiederum die Erweiterungsmethode [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-303">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="3d8b5-304">Benannte Optionen Unterscheidung nach Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-304">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="3d8b5-305">Die Beispielanwendung greift mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> auf die benannten Optionen zu ( *Pages/Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-305">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="3d8b5-306">Bei der Ausführung der Beispielanwendung werden die benannten Optionen zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-306">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="3d8b5-307">`named_options_1`-Werte werden von der Konfiguration bereitgestellt. Sie werden aus der *appsettings.json* -Datei geladen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-307">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="3d8b5-308">`named_options_2`-Werte werden bereitgestellt vom:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-308">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="3d8b5-309">`named_options_2`-Delegat in `ConfigureServices` für `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-309">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="3d8b5-310">Der Standardwert für `Option2` wird von der `MyOptions`-Klasse bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-310">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="3d8b5-311">Konfigurieren aller Optionen mit der ConfigureAll-Methode</span><span class="sxs-lookup"><span data-stu-id="3d8b5-311">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="3d8b5-312">Konfigurieren Sie alle Optionsinstanzen mit der <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-312">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="3d8b5-313">Der folgende Code konfiguriert `Option1` für alle Konfigurationsinstanzen mit einem gemeinsamen Wert.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-313">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="3d8b5-314">Fügen Sie der `Startup.ConfigureServices`-Methode manuell den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-314">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="3d8b5-315">Das Ausführen der Beispielanwendung nach dem Hinzufügen des Codes führt zu folgendem Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-315">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="3d8b5-316">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-316">All options are named instances.</span></span> <span data-ttu-id="3d8b5-317">Vorhandene <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als ob sie auf die `Options.DefaultName`-Instanz abzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-317">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="3d8b5-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-318"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="3d8b5-319">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-319">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="3d8b5-320">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-320">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="3d8b5-321">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="3d8b5-321">OptionsBuilder API</span></span>

<span data-ttu-id="3d8b5-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-322"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="3d8b5-323">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-323">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="3d8b5-324">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-324">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="3d8b5-325">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-325">Use DI services to configure options</span></span>

<span data-ttu-id="3d8b5-326">Beim Konfigurieren von Optionen haben Sie zwei Möglichkeiten, auf andere Dienste aus Dependency Injections zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-326">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="3d8b5-327">Übergeben Sie einen Konfigurationsdelegaten an [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-327">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="3d8b5-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) stellt Überladungen von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es Ihnen ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-328">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="3d8b5-329">Erstellen Ihres eigenen Typs, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und Registrieren des Typs als Dienst.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-329">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="3d8b5-330">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-330">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="3d8b5-331">Selbst einen eigenen Typ zu erstellen ist gleichbedeutend mit dem, was das Framework für Sie übernimmt, wenn Sie [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-331">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="3d8b5-332">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-332">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-validation"></a><span data-ttu-id="3d8b5-333">Überprüfung von Optionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-333">Options validation</span></span>

<span data-ttu-id="3d8b5-334">Sie können Optionen überprüfen, wenn diese konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-334">Options validation allows you to validate options when options are configured.</span></span> <span data-ttu-id="3d8b5-335">Rufen Sie `Validate` mit einer Überprüfungsmethode auf, die `true` zurückgibt, wenn die Optionen gültig sind, und `false` zurückgibt, wenn sie es nicht sind:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-335">Call `Validate` with a validation method that returns `true` if options are valid and `false` if they aren't valid:</span></span>

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

<span data-ttu-id="3d8b5-336">Im vorherigen Beispiel wurde die benannte Optionsinstanz auf `optionalOptionsName` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-336">The preceding example sets the named options instance to `optionalOptionsName`.</span></span> <span data-ttu-id="3d8b5-337">Die Standardoptionsinstanz ist `Options.DefaultName`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-337">The default options instance is `Options.DefaultName`.</span></span>

<span data-ttu-id="3d8b5-338">Überprüfungen werden ausgeführt, wenn die Optionsinstanz erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-338">Validation runs when the options instance is created.</span></span> <span data-ttu-id="3d8b5-339">Eine Optionsinstanz besteht die Überprüfung beim ersten Zugriff garantiert.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-339">An options instance is guaranteed to pass validation the first time it's accessed.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3d8b5-340">Die Überprüfung von Optionen schützt nicht vor Änderungen an Optionen nach der Erstellung der Optionsinstanz.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-340">Options validation doesn't guard against options modifications after the options instance is created.</span></span> <span data-ttu-id="3d8b5-341">Beispielsweise werden `IOptionsSnapshot`-Optionen einmal pro Anforderung erstellt und überprüft, wenn zum ersten Mal auf die Optionen zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-341">For example, `IOptionsSnapshot` options are created and validated once per request when the options are first accessed.</span></span> <span data-ttu-id="3d8b5-342">Die `IOptionsSnapshot`-Optionen werden bei nachfolgenden Zugriffsversuchen *für dieselbe Anforderung* nicht erneut überprüft.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-342">The `IOptionsSnapshot` options aren't validated again on subsequent access attempts *for the same request*.</span></span>

<span data-ttu-id="3d8b5-343">Die `Validate`-Methode akzeptiert ein `Func<TOptions, bool>`-Element.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-343">The `Validate` method accepts a `Func<TOptions, bool>`.</span></span> <span data-ttu-id="3d8b5-344">Um die Überprüfung vollständig anzupassen, implementieren Sie `IValidateOptions<TOptions>`. Dies ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-344">To fully customize validation, implement `IValidateOptions<TOptions>`, which allows:</span></span>

* <span data-ttu-id="3d8b5-345">Eine Überprüfung von mehreren Optionstypen: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span><span class="sxs-lookup"><span data-stu-id="3d8b5-345">Validation of multiple options types: `class ValidateTwo : IValidateOptions<Option1>, IValidationOptions<Option2>`</span></span>
* <span data-ttu-id="3d8b5-346">Eine Überprüfung, die von einem anderen Optionstyp abhängig ist: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span><span class="sxs-lookup"><span data-stu-id="3d8b5-346">Validation that depends on another option type: `public DependsOnAnotherOptionValidator(IOptionsMonitor<AnotherOption> options)`</span></span>

<span data-ttu-id="3d8b5-347">`IValidateOptions` validiert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-347">`IValidateOptions` validates:</span></span>

* <span data-ttu-id="3d8b5-348">Eine bestimmte benannte Optionsinstanz.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-348">A specific named options instance.</span></span>
* <span data-ttu-id="3d8b5-349">Alle Optionen, wenn `name` den Wert `null` aufweist.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-349">All options when `name` is `null`.</span></span>

<span data-ttu-id="3d8b5-350">Geben Sie ein `ValidateOptionsResult` aus Ihrer Implementierung der Schnittstelle zurück:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-350">Return a `ValidateOptionsResult` from your implementation of the interface:</span></span>

```csharp
public interface IValidateOptions<TOptions> where TOptions : class
{
    ValidateOptionsResult Validate(string name, TOptions options);
}
```

<span data-ttu-id="3d8b5-351">Die Überprüfung auf Basis von Datenanmerkungen ist über das Paket [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) durch Aufrufen der <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*>-Methode in `OptionsBuilder<TOptions>` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-351">Data Annotation-based validation is available from the [Microsoft.Extensions.Options.DataAnnotations](https://www.nuget.org/packages/Microsoft.Extensions.Options.DataAnnotations) package by calling the <xref:Microsoft.Extensions.DependencyInjection.OptionsBuilderDataAnnotationsExtensions.ValidateDataAnnotations*> method on `OptionsBuilder<TOptions>`.</span></span> <span data-ttu-id="3d8b5-352">`Microsoft.Extensions.Options.DataAnnotations` ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-352">`Microsoft.Extensions.Options.DataAnnotations` is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

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

<span data-ttu-id="3d8b5-353">Die vorzeitige Überprüfung (Fail-fast beim Start) wird für ein späteres Release in Erwägung.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-353">Eager validation (fail fast at startup) is under consideration for a future release.</span></span>

## <a name="options-post-configuration"></a><span data-ttu-id="3d8b5-354">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="3d8b5-354">Options post-configuration</span></span>

<span data-ttu-id="3d8b5-355">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-355">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="3d8b5-356">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-356">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3d8b5-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-357"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3d8b5-358">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-358">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="3d8b5-359">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="3d8b5-359">Accessing options during startup</span></span>

<span data-ttu-id="3d8b5-360"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-360"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="3d8b5-361">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-361">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3d8b5-362">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-362">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="3d8b5-363">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-363">The options pattern uses classes to represent groups of related settings.</span></span> <span data-ttu-id="3d8b5-364">Wenn [Konfigurationseinstellungen](xref:fundamentals/configuration/index) nach Szenario in separate Klassen isoliert werden, entspricht die Anwendung zwei wichtigen Prinzipien der Softwareentwicklung:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-364">When [configuration settings](xref:fundamentals/configuration/index) are isolated by scenario into separate classes, the app adheres to two important software engineering principles:</span></span>

* <span data-ttu-id="3d8b5-365">Das [Schnittstellentrennungsprinzip oder die Kapselung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Szenarios (Klassen), die von Konfigurationseinstellungen abhängen, sind nur von den Konfigurationseinstellungen abhängig, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-365">The [Interface Segregation Principle (ISP) or Encapsulation](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation): Scenarios (classes) that depend on configuration settings depend only on the configuration settings that they use.</span></span>
* <span data-ttu-id="3d8b5-366">[Trennung von Zuständigkeiten:](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) Einstellungen für verschiedene App-Komponenten sind nicht voneinander abhängig und nicht aneinander gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-366">[Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns): Settings for different parts of the app aren't dependent or coupled to one another.</span></span>

<span data-ttu-id="3d8b5-367">Optionen bieten auch einen Mechanismus, um Konfigurationsdaten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-367">Options also provide a mechanism to validate configuration data.</span></span> <span data-ttu-id="3d8b5-368">Weitere Informationen finden Sie im Abschnitt [Optionsvalidierung](#options-validation).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-368">For more information, see the [Options validation](#options-validation) section.</span></span>

<span data-ttu-id="3d8b5-369">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3d8b5-369">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/options/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3d8b5-370">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-370">Prerequisites</span></span>

<span data-ttu-id="3d8b5-371">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-371">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Options.ConfigurationExtensions](https://www.nuget.org/packages/Microsoft.Extensions.Options.ConfigurationExtensions/) package.</span></span>

## <a name="options-interfaces"></a><span data-ttu-id="3d8b5-372">Optionenschnittstellen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-372">Options interfaces</span></span>

<span data-ttu-id="3d8b5-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> wird verwendet, um Optionen abzurufen und Benachrichtigungen über Optionen für `TOptions`-Instanzen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-373"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> is used to retrieve options and manage options notifications for `TOptions` instances.</span></span> <span data-ttu-id="3d8b5-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> unterstützt die folgenden Szenarios:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-374"><xref:Microsoft.Extensions.Options.IOptionsMonitor%601> supports the following scenarios:</span></span>

* <span data-ttu-id="3d8b5-375">Änderungsbenachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-375">Change notifications</span></span>
* [<span data-ttu-id="3d8b5-376">Benannte Optionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-376">Named options</span></span>](#named-options-support-with-iconfigurenamedoptions)
* [<span data-ttu-id="3d8b5-377">Erneut ladbare Konfiguration</span><span class="sxs-lookup"><span data-stu-id="3d8b5-377">Reloadable configuration</span></span>](#reload-configuration-data-with-ioptionssnapshot)
* <span data-ttu-id="3d8b5-378">Selektive Optionsvalidierung (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span><span class="sxs-lookup"><span data-stu-id="3d8b5-378">Selective options invalidation (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601>)</span></span>

<span data-ttu-id="3d8b5-379">Mit [Postkonfigurationsszenarien](#options-post-configuration) können Sie Optionen festlegen oder ändern, nachdem alle <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfigurationen durchgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-379">[Post-configuration](#options-post-configuration) scenarios allow you to set or change options after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs.</span></span>

<span data-ttu-id="3d8b5-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> ist für das Erstellen neuer Optionsinstanzen zuständig.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-380"><xref:Microsoft.Extensions.Options.IOptionsFactory%601> is responsible for creating new options instances.</span></span> <span data-ttu-id="3d8b5-381">Es verfügt über eine einzelne <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-381">It has a single <xref:Microsoft.Extensions.Options.IOptionsFactory`1.Create*> method.</span></span> <span data-ttu-id="3d8b5-382">Die Standardimplementierung akzeptiert alle registrierten <xref:Microsoft.Extensions.Options.IConfigureOptions%601> und <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> und führt alle Konfigurationen zuerst und die Postkonfigurationen danach aus.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-382">The default implementation takes all registered <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> and runs all the configurations first, followed by the post-configuration.</span></span> <span data-ttu-id="3d8b5-383">Es wird zwischen <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> und <xref:Microsoft.Extensions.Options.IConfigureOptions%601> unterschieden, und es werden nur die entsprechenden Schnittstellen aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-383">It distinguishes between <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and <xref:Microsoft.Extensions.Options.IConfigureOptions%601> and only calls the appropriate interface.</span></span>

<span data-ttu-id="3d8b5-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> wird von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> zum Zwischenspeichern der `TOptions`-Instanzen verwendet.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-384"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> is used by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to cache `TOptions` instances.</span></span> <span data-ttu-id="3d8b5-385"><xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> erklärt Optionsinstanzen im Monitor für ungültig, sodass der Wert neu berechnet wird (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-385">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache%601> invalidates options instances in the monitor so that the value is recomputed (<xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryRemove*>).</span></span> <span data-ttu-id="3d8b5-386">Werte können manuell mit <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*> eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-386">Values can be manually introduced with <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.TryAdd*>.</span></span> <span data-ttu-id="3d8b5-387">Die <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*>-Methode wird verwendet, wenn alle benannten Instanzen bei Bedarf neu erstellt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-387">The <xref:Microsoft.Extensions.Options.IOptionsMonitorCache`1.Clear*> method is used when all named instances should be recreated on demand.</span></span>

<span data-ttu-id="3d8b5-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> ist nützlich in Szenarien, in denen Optionen bei jeder Anforderung neu berechnet werden sollten.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-388"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is useful in scenarios where options should be recomputed on every request.</span></span> <span data-ttu-id="3d8b5-389">Weitere Informationen finden Sie im Abschnitt [Neuladen der Konfigurationsdaten mit IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-389">For more information, see the [Reload configuration data with IOptionsSnapshot](#reload-configuration-data-with-ioptionssnapshot) section.</span></span>

<span data-ttu-id="3d8b5-390"><xref:Microsoft.Extensions.Options.IOptions%601> kann zum Unterstützen von Optionen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-390"><xref:Microsoft.Extensions.Options.IOptions%601> can be used to support options.</span></span> <span data-ttu-id="3d8b5-391">Allerdings unterstützt <xref:Microsoft.Extensions.Options.IOptions%601> nicht die oben beschriebenen Szenarios von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-391">However, <xref:Microsoft.Extensions.Options.IOptions%601> doesn't support the preceding scenarios of <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span> <span data-ttu-id="3d8b5-392">Sie können <xref:Microsoft.Extensions.Options.IOptions%601> weiterhin in bestehenden Frameworks und Bibliotheken verwenden, die bereits die <xref:Microsoft.Extensions.Options.IOptions%601>-Schnittstelle verwenden und nicht die von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> bereitgestellten Szenarios benötigen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-392">You may continue to use <xref:Microsoft.Extensions.Options.IOptions%601> in existing frameworks and libraries that already use the <xref:Microsoft.Extensions.Options.IOptions%601> interface and don't require the scenarios provided by <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>.</span></span>

## <a name="general-options-configuration"></a><span data-ttu-id="3d8b5-393">Allgemeine Optionskonfiguration</span><span class="sxs-lookup"><span data-stu-id="3d8b5-393">General options configuration</span></span>

<span data-ttu-id="3d8b5-394">Die allgemeine Optionskonfiguration wird als Beispiel 1 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-394">General options configuration is demonstrated as Example 1 in the sample app.</span></span>

<span data-ttu-id="3d8b5-395">Eine Optionsklasse muss nicht abstrakt sein und über einen öffentlichen parameterlosen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-395">An options class must be non-abstract with a public parameterless constructor.</span></span> <span data-ttu-id="3d8b5-396">Die folgende Klasse `MyOptions` verfügt über die zwei Eigenschaften: `Option1` und `Option2`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-396">The following class, `MyOptions`, has two properties, `Option1` and `Option2`.</span></span> <span data-ttu-id="3d8b5-397">Das Festlegen von Standardwerten ist optional, aber der Klassenkonstruktor im folgenden Beispiel legt den Standardwert von `Option1` fest.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-397">Setting default values is optional, but the class constructor in the following example sets the default value of `Option1`.</span></span> <span data-ttu-id="3d8b5-398">`Option2` hat den Standardwert festgelegt, indem die Eigenschaft direkt initialisiert wurde ( *Models/MyOptions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-398">`Option2` has a default value set by initializing the property directly ( *Models/MyOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptions.cs?name=snippet1)]

<span data-ttu-id="3d8b5-399">Die `MyOptions`-Klasse wird zum Dienstcontainer mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-399">The `MyOptions` class is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example1)]

<span data-ttu-id="3d8b5-400">Das folgende Seitenmodel verwendet [konstruktorbasierte Dependency Injection](xref:mvc/controllers/dependency-injection) mit <xref:Microsoft.Extensions.Options.IOptionsMonitor%601>, um auf die Einstellungen zugreifen zu können ( *Pages/Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-400">The following page model uses [constructor dependency injection](xref:mvc/controllers/dependency-injection) with <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> to access the settings ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example1)]

<span data-ttu-id="3d8b5-401">Die *appsettings.json* -Datei des Beispiels gibt Werte für `option1` und `option2` an:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-401">The sample's *appsettings.json* file specifies values for `option1` and `option2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=2-3)]

<span data-ttu-id="3d8b5-402">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-402">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
option1 = value1_from_json, option2 = -1
```

> [!NOTE]
> <span data-ttu-id="3d8b5-403">Wenn Sie eine benutzerdefinierte <xref:System.Configuration.ConfigurationBuilder>-Klasse verwenden, um Konfigurationsoptionen aus einer Einstellungsdatei zu laden, bestätigen Sie, dass der Basispfad ordnungsgemäß festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-403">When using a custom <xref:System.Configuration.ConfigurationBuilder> to load options configuration from a settings file, confirm that the base path is set correctly:</span></span>
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
> <span data-ttu-id="3d8b5-404">Sie müssen den Basispfad nicht explizit festlegen, wenn Sie Konfigurationsoptionen über <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> aus der Einstellungsdatei laden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-404">Explicitly setting the base path isn't required when loading options configuration from the settings file via <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

## <a name="configure-simple-options-with-a-delegate"></a><span data-ttu-id="3d8b5-405">Konfigurieren von einfachen Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="3d8b5-405">Configure simple options with a delegate</span></span>

<span data-ttu-id="3d8b5-406">Das Konfigurieren von einfachen Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-406">Configuring simple options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="3d8b5-407">Verwenden Sie einen Delegaten zum Festlegen von Optionswerten.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-407">Use a delegate to set options values.</span></span> <span data-ttu-id="3d8b5-408">Die Beispielanwendung verwendet die `MyOptionsWithDelegateConfig`-Klasse ( *Models/MyOptionsWithDelegateConfig.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-408">The sample app uses the `MyOptionsWithDelegateConfig` class ( *Models/MyOptionsWithDelegateConfig.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MyOptionsWithDelegateConfig.cs?name=snippet1)]

<span data-ttu-id="3d8b5-409">Im folgenden Code wird ein zweiter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-409">In the following code, a second <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3d8b5-410">Er verwendet einen Delegaten zum Konfigurieren der Bindung mit `MyOptionsWithDelegateConfig`:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-410">It uses a delegate to configure the binding with `MyOptionsWithDelegateConfig`:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example2)]

<span data-ttu-id="3d8b5-411">*Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="3d8b5-411">*Index.cshtml.cs* :</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=3,9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example2)]

<span data-ttu-id="3d8b5-412">Sie können mehrere Konfigurationsanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-412">You can add multiple configuration providers.</span></span> <span data-ttu-id="3d8b5-413">Konfigurationsanbieter sind über NuGet-Pakete verfügbar und werden in der Reihenfolge ihrer Registrierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-413">Configuration providers are available from NuGet packages and are applied in the order that they're registered.</span></span> <span data-ttu-id="3d8b5-414">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-414">For more information, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="3d8b5-415">Jeder Aufruf von <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> fügt einen <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzu.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-415">Each call to <xref:Microsoft.Extensions.Options.IConfigureOptions%601.Configure*> adds an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service to the service container.</span></span> <span data-ttu-id="3d8b5-416">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` beide in *appsettings.json* angegeben, aber die Werte von `Option1` und `Option2` werden vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-416">In the preceding example, the values of `Option1` and `Option2` are both specified in *appsettings.json* , but the values of `Option1` and `Option2` are overridden by the configured delegate.</span></span>

<span data-ttu-id="3d8b5-417">Wenn mehr als ein Konfigurationsdienst aktiviert ist, *gewinnt* die letzte angegebene Konfigurationsquelle und legt den Konfigurationswert fest.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-417">When more than one configuration service is enabled, the last configuration source specified *wins* and sets the configuration value.</span></span> <span data-ttu-id="3d8b5-418">Wenn die Anwendung ausgeführt wird, gibt die `OnGet`-Methode des Seitenmodells eine Zeichenfolge zurück, die die Werte der Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-418">When the app is run, the page model's `OnGet` method returns a string showing the option class values:</span></span>

```html
delegate_option1 = value1_configured_by_delegate, delegate_option2 = 500
```

## <a name="suboptions-configuration"></a><span data-ttu-id="3d8b5-419">Konfigurieren von Unteroptionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-419">Suboptions configuration</span></span>

<span data-ttu-id="3d8b5-420">Das Konfigurieren von Unteroptionen wird als Beispiel 3 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-420">Suboptions configuration is demonstrated as Example 3 in the sample app.</span></span>

<span data-ttu-id="3d8b5-421">Anwendungen sollten Optionsklassen erstellen, die für bestimmte Szenariogruppen (Klassen) in der Anwendung gelten.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-421">Apps should create options classes that pertain to specific scenario groups (classes) in the app.</span></span> <span data-ttu-id="3d8b5-422">Die Komponenten der Anwendung, die Konfigurationswerte erfordern, sollten nur über Zugriff auf die Konfigurationswerte verfügen, die sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-422">Parts of the app that require configuration values should only have access to the configuration values that they use.</span></span>

<span data-ttu-id="3d8b5-423">Beim Binden von Optionen zur Konfiguration ist jede Eigenschaft im Optionstyp an einen Konfigurationsschlüssel des `property[:sub-property:]`-Formulars gebunden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-423">When binding options to configuration, each property in the options type is bound to a configuration key of the form `property[:sub-property:]`.</span></span> <span data-ttu-id="3d8b5-424">Die `MyOptions.Option1`-Eigenschaft ist beispielsweise an den Schlüssel `Option1` gebunden, der von der `option1`-Eigenschaft in *appsettings.json* gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-424">For example, the `MyOptions.Option1` property is bound to the key `Option1`, which is read from the `option1` property in *appsettings.json*.</span></span>

<span data-ttu-id="3d8b5-425">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-425">In the following code, a third <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="3d8b5-426">Dadurch wird `MySubOptions` an den Abschnitt `subsection` der *appsettings.json* -Datei gebunden:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-426">It binds `MySubOptions` to the section `subsection` of the *appsettings.json* file:</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example3)]

<span data-ttu-id="3d8b5-427">Die `GetSection`-Methode erfordert den Namespace <xref:Microsoft.Extensions.Configuration?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-427">The `GetSection` method requires the <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="3d8b5-428">Die *appsettings.json* -Datei des Beispiels definiert einen `subsection`-Member mit Schlüsseln für `suboption1` und `suboption2`:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-428">The sample's *appsettings.json* file defines a `subsection` member with keys for `suboption1` and `suboption2`:</span></span>

[!code-json[](options/samples/2.x/OptionsSample/appsettings.json?highlight=4-7)]

<span data-ttu-id="3d8b5-429">Die `MySubOptions`-Klasse definiert die Eigenschaften `SubOption1` und `SubOption2` zur Aufnahme der Optionswerte ( *Models/MySubOptions.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-429">The `MySubOptions` class defines properties, `SubOption1` and `SubOption2`, to hold the options values ( *Models/MySubOptions.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Models/MySubOptions.cs?name=snippet1)]

<span data-ttu-id="3d8b5-430">Die `OnGet`-Methode des Seitenmodells gibt eine Zeichenfolge mit den Optionswerten ( *Pages/Index.cshtml.cs* ) zurück:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-430">The page model's `OnGet` method returns a string with the options values ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=4,10)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example3)]

<span data-ttu-id="3d8b5-431">Wenn die App ausgeführt wird, gibt die `OnGet`-Methode eine Zeichenfolge zurück, die die Werte der untergeordneten Optionsklasse anzeigt:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-431">When the app is run, the `OnGet` method returns a string showing the suboption class values:</span></span>

```html
subOption1 = subvalue1_from_json, subOption2 = 200
```

## <a name="options-provided-by-a-view-model-or-with-direct-view-injection"></a><span data-ttu-id="3d8b5-432">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden</span><span class="sxs-lookup"><span data-stu-id="3d8b5-432">Options provided by a view model or with direct view injection</span></span>

<span data-ttu-id="3d8b5-433">Optionen, die durch ein Ansichtsmodell oder mit direkter View Injection bereitgestellt werden, werden im Beispiel 4 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-433">Options provided by a view model or with direct view injection is demonstrated as Example 4 in the sample app.</span></span>

<span data-ttu-id="3d8b5-434">Optionen können in einem Ansichtsmodell oder durch direkte Injection von <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in eine Ansicht ( *Pages/Index.cshtml.cs* ) angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-434">Options can be supplied in a view model or by injecting <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> directly into a view ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=9)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=2,8)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example4)]

<span data-ttu-id="3d8b5-435">Die Beispiel-App zeigt das Einfügen von `IOptionsMonitor<MyOptions>` mit einer `@inject`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-435">The sample app shows how to inject `IOptionsMonitor<MyOptions>` with an `@inject` directive:</span></span>

[!code-cshtml[](options/samples/2.x/OptionsSample/Pages/Index.cshtml?range=1-10&highlight=4)]

<span data-ttu-id="3d8b5-436">Wenn die App ausgeführt wird, werden die Optionswerte in der gerenderten Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-436">When the app is run, the options values are shown in the rendered page:</span></span>

![Optionswerte Option1: value1_from_json und Option2: -1 werden aus dem Modell geladen und in die Ansicht eingefügt.](options/_static/view.png)

## <a name="reload-configuration-data-with-ioptionssnapshot"></a><span data-ttu-id="3d8b5-438">Neuladen der Konfigurationsdaten mit IOptionsSnapshot</span><span class="sxs-lookup"><span data-stu-id="3d8b5-438">Reload configuration data with IOptionsSnapshot</span></span>

<span data-ttu-id="3d8b5-439">Das Neuladen der Konfigurationsdaten mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> wird im Beispiel 5 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-439">Reloading configuration data with <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is demonstrated in Example 5 in the sample app.</span></span>

<span data-ttu-id="3d8b5-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> unterstützt das Neuladen von Optionen mit minimalem Verarbeitungsaufwand.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-440"><xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> supports reloading options with minimal processing overhead.</span></span>

<span data-ttu-id="3d8b5-441">Optionen werden einmal pro Anforderung berechnet. Dies geschieht, wenn auf sie zugegriffen wird und sie für die Dauer der Anforderung zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-441">Options are computed once per request when accessed and cached for the lifetime of the request.</span></span>

<span data-ttu-id="3d8b5-442">Im folgenden Beispiel wird veranschaulicht, wie eine neue <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> nach den *appsettings.json* -Änderungen erstellt wird ( *Pages/Index.cshtml.cs* ).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-442">The following example demonstrates how a new <xref:Microsoft.Extensions.Options.IOptionsSnapshot%601> is created after *appsettings.json* changes ( *Pages/Index.cshtml.cs* ).</span></span> <span data-ttu-id="3d8b5-443">Mehrere Anforderungen an den Server geben konstante Werte zurück, die von der *appsettings.json* -Datei bereitgestellt werden, bis die Datei geändert wird und die Konfiguration neu lädt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-443">Multiple requests to the server return constant values provided by the *appsettings.json* file until the file is changed and configuration reloads.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=12)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=5,11)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example5)]

<span data-ttu-id="3d8b5-444">Die folgende Abbildung zeigt die anfänglichen `option1`- und `option2`-Werte, die aus der *appsettings.json* -Datei geladen werden:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-444">The following image shows the initial `option1` and `option2` values loaded from the *appsettings.json* file:</span></span>

```html
snapshot option1 = value1_from_json, snapshot option2 = -1
```

<span data-ttu-id="3d8b5-445">Ändern Sie die Werte in der *appsettings.json* -Datei in `value1_from_json UPDATED` und `200`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-445">Change the values in the *appsettings.json* file to `value1_from_json UPDATED` and `200`.</span></span> <span data-ttu-id="3d8b5-446">Speichern Sie die *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-446">Save the *appsettings.json* file.</span></span> <span data-ttu-id="3d8b5-447">Aktualisieren Sie den Browser, um festzustellen, ob die Optionswerte aktualisiert wurden:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-447">Refresh the browser to see that the options values are updated:</span></span>

```html
snapshot option1 = value1_from_json UPDATED, snapshot option2 = 200
```

## <a name="named-options-support-with-iconfigurenamedoptions"></a><span data-ttu-id="3d8b5-448">Unterstützung für benannte Optionen mit IConfigureNamedOptions</span><span class="sxs-lookup"><span data-stu-id="3d8b5-448">Named options support with IConfigureNamedOptions</span></span>

<span data-ttu-id="3d8b5-449">Unterstützung für benannte Optionen mit <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> wird als Beispiel 6 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-449">Named options support with <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> is demonstrated as Example 6 in the sample app.</span></span>

<span data-ttu-id="3d8b5-450">Die Unterstützung für benannte Optionen ermöglicht es der Anwendung, zwischen den Konfigurationen benannter Optionen zu unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-450">Named options support allows the app to distinguish between named options configurations.</span></span> <span data-ttu-id="3d8b5-451">In der Beispiel-App werden benannte Optionen mit [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*) deklariert. Dadurch wird wiederum die Erweiterungsmethode [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-451">In the sample app, named options are declared with [OptionsServiceCollectionExtensions.Configure](xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.Configure*), which calls the [ConfigureNamedOptions\<TOptions>.Configure](xref:Microsoft.Extensions.Options.ConfigureNamedOptions`1.Configure*) extension method.</span></span> <span data-ttu-id="3d8b5-452">Benannte Optionen Unterscheidung nach Groß-/Kleinschreibung.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-452">Named options are case sensitive.</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Startup.cs?name=snippet_Example6)]

<span data-ttu-id="3d8b5-453">Die Beispielanwendung greift mit <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> auf die benannten Optionen zu ( *Pages/Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="3d8b5-453">The sample app accesses the named options with <xref:Microsoft.Extensions.Options.IOptionsSnapshot`1.Get*> ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?range=13-14)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet2&highlight=6,12-13)]

[!code-csharp[](options/samples/2.x/OptionsSample/Pages/Index.cshtml.cs?name=snippet_Example6)]

<span data-ttu-id="3d8b5-454">Bei der Ausführung der Beispielanwendung werden die benannten Optionen zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-454">Running the sample app, the named options are returned:</span></span>

```html
named_options_1: option1 = value1_from_json, option2 = -1
named_options_2: option1 = named_options_2_value1_from_action, option2 = 5
```

<span data-ttu-id="3d8b5-455">`named_options_1`-Werte werden von der Konfiguration bereitgestellt. Sie werden aus der *appsettings.json* -Datei geladen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-455">`named_options_1` values are provided from configuration, which are loaded from the *appsettings.json* file.</span></span> <span data-ttu-id="3d8b5-456">`named_options_2`-Werte werden bereitgestellt vom:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-456">`named_options_2` values are provided by:</span></span>

* <span data-ttu-id="3d8b5-457">`named_options_2`-Delegat in `ConfigureServices` für `Option1`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-457">The `named_options_2` delegate in `ConfigureServices` for `Option1`.</span></span>
* <span data-ttu-id="3d8b5-458">Der Standardwert für `Option2` wird von der `MyOptions`-Klasse bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-458">The default value for `Option2` provided by the `MyOptions` class.</span></span>

## <a name="configure-all-options-with-the-configureall-method"></a><span data-ttu-id="3d8b5-459">Konfigurieren aller Optionen mit der ConfigureAll-Methode</span><span class="sxs-lookup"><span data-stu-id="3d8b5-459">Configure all options with the ConfigureAll method</span></span>

<span data-ttu-id="3d8b5-460">Konfigurieren Sie alle Optionsinstanzen mit der <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-460">Configure all options instances with the <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> method.</span></span> <span data-ttu-id="3d8b5-461">Der folgende Code konfiguriert `Option1` für alle Konfigurationsinstanzen mit einem gemeinsamen Wert.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-461">The following code configures `Option1` for all configuration instances with a common value.</span></span> <span data-ttu-id="3d8b5-462">Fügen Sie der `Startup.ConfigureServices`-Methode manuell den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-462">Add the following code manually to the `Startup.ConfigureServices` method:</span></span>

```csharp
services.ConfigureAll<MyOptions>(myOptions => 
{
    myOptions.Option1 = "ConfigureAll replacement value";
});
```

<span data-ttu-id="3d8b5-463">Das Ausführen der Beispielanwendung nach dem Hinzufügen des Codes führt zu folgendem Ergebnis:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-463">Running the sample app after adding the code produces the following result:</span></span>

```html
named_options_1: option1 = ConfigureAll replacement value, option2 = -1
named_options_2: option1 = ConfigureAll replacement value, option2 = 5
```

> [!NOTE]
> <span data-ttu-id="3d8b5-464">Alle Optionen sind benannte Instanzen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-464">All options are named instances.</span></span> <span data-ttu-id="3d8b5-465">Vorhandene <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Instanzen werden behandelt, als ob sie auf die `Options.DefaultName`-Instanz abzielen. Diese ist `string.Empty`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-465">Existing <xref:Microsoft.Extensions.Options.IConfigureOptions%601> instances are treated as targeting the `Options.DefaultName` instance, which is `string.Empty`.</span></span> <span data-ttu-id="3d8b5-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert auch <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-466"><xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> also implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601>.</span></span> <span data-ttu-id="3d8b5-467">Die standardmäßige Implementierung von <xref:Microsoft.Extensions.Options.IOptionsFactory%601> verfügt über eine Logik, um jede einzelne entsprechend zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-467">The default implementation of the <xref:Microsoft.Extensions.Options.IOptionsFactory%601> has logic to use each appropriately.</span></span> <span data-ttu-id="3d8b5-468">Die benannte Option `null` wird verwendet, um auf alle benannten Instanzen anstelle einer bestimmten benannten Instanz abzuzielen (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> und <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> verwenden diese Konvention).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-468">The `null` named option is used to target all of the named instances instead of a specific named instance (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.ConfigureAll*> and <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> use this convention).</span></span>

## <a name="optionsbuilder-api"></a><span data-ttu-id="3d8b5-469">OptionsBuilder-API</span><span class="sxs-lookup"><span data-stu-id="3d8b5-469">OptionsBuilder API</span></span>

<span data-ttu-id="3d8b5-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> dient zum Konfigurieren von `TOptions`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-470"><xref:Microsoft.Extensions.Options.OptionsBuilder%601> is used to configure `TOptions` instances.</span></span> <span data-ttu-id="3d8b5-471">`OptionsBuilder` optimiert die Erstellung von benannten Optionen, da es sich nur um einen einzelnen Parameter für den ersten `AddOptions<TOptions>(string optionsName)`-Aufruf handelt statt um alle nachfolgenden Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-471">`OptionsBuilder` streamlines creating named options as it's only a single parameter to the initial `AddOptions<TOptions>(string optionsName)` call instead of appearing in all of the subsequent calls.</span></span> <span data-ttu-id="3d8b5-472">Die Optionsvalidierung und die `ConfigureOptions`-Überladungen, die Dienstabhängigkeiten akzeptieren, sind nur über `OptionsBuilder` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-472">Options validation and the `ConfigureOptions` overloads that accept service dependencies are only available via `OptionsBuilder`.</span></span>

```csharp
// Options.DefaultName = "" is used.
services.AddOptions<MyOptions>().Configure(o => o.Property = "default");

services.AddOptions<MyOptions>("optionalName")
    .Configure(o => o.Property = "named");
```

## <a name="use-di-services-to-configure-options"></a><span data-ttu-id="3d8b5-473">Verwenden von DI-Diensten zum Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-473">Use DI services to configure options</span></span>

<span data-ttu-id="3d8b5-474">Beim Konfigurieren von Optionen haben Sie zwei Möglichkeiten, auf andere Dienste aus Dependency Injections zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-474">You can access other services from dependency injection while configuring options in two ways:</span></span>

* <span data-ttu-id="3d8b5-475">Übergeben Sie einen Konfigurationsdelegaten an [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) in [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span><span class="sxs-lookup"><span data-stu-id="3d8b5-475">Pass a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) on [OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1).</span></span> <span data-ttu-id="3d8b5-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) stellt Überladungen von [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) zur Verfügung, die es Ihnen ermöglichen, bis zu fünf Dienste zu verwenden, um Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-476">[OptionsBuilder\<TOptions>](xref:Microsoft.Extensions.Options.OptionsBuilder`1) provides overloads of [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) that allow you to use up to five services to configure options:</span></span>

  ```csharp
  services.AddOptions<MyOptions>("optionalName")
      .Configure<Service1, Service2, Service3, Service4, Service5>(
          (o, s, s2, s3, s4, s5) => 
              o.Property = DoSomethingWith(s, s2, s3, s4, s5));
  ```

* <span data-ttu-id="3d8b5-477">Erstellen Ihres eigenen Typs, der <xref:Microsoft.Extensions.Options.IConfigureOptions%601> oder <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> implementiert, und Registrieren des Typs als Dienst.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-477">Create your own type that implements <xref:Microsoft.Extensions.Options.IConfigureOptions%601> or <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> and register the type as a service.</span></span>

<span data-ttu-id="3d8b5-478">Es empfiehlt sich das Übergeben eines Konfigurationsdelegaten an [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), da das Erstellen eines Diensts etwas komplexer ist.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-478">We recommend passing a configuration delegate to [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*), since creating a service is more complex.</span></span> <span data-ttu-id="3d8b5-479">Selbst einen eigenen Typ zu erstellen ist gleichbedeutend mit dem, was das Framework für Sie übernimmt, wenn Sie [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) verwenden.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-479">Creating your own type is equivalent to what the framework does for you when you use [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*).</span></span> <span data-ttu-id="3d8b5-480">Wenn [Konfigurieren](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) aufgerufen wird, wird eine vorübergehende, generische <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601> registriert, die über einen Konstruktor verfügt, der die angegeben generischen Diensttypen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-480">Calling [Configure](xref:Microsoft.Extensions.Options.OptionsBuilder`1.Configure*) registers a transient generic <xref:Microsoft.Extensions.Options.IConfigureNamedOptions%601>, which has a constructor that accepts the generic service types specified.</span></span> 

## <a name="options-post-configuration"></a><span data-ttu-id="3d8b5-481">Optionen für die Postkonfiguration</span><span class="sxs-lookup"><span data-stu-id="3d8b5-481">Options post-configuration</span></span>

<span data-ttu-id="3d8b5-482">Legen Sie die Postkonfiguration mit <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601> fest.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-482">Set post-configuration with <xref:Microsoft.Extensions.Options.IPostConfigureOptions%601>.</span></span> <span data-ttu-id="3d8b5-483">Die Postkonfiguration erfolgt, nachdem die gesamte <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Konfiguration abgeschlossen ist:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-483">Post-configuration runs after all <xref:Microsoft.Extensions.Options.IConfigureOptions%601> configuration occurs:</span></span>

```csharp
services.PostConfigure<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3d8b5-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> ist nach dem Konfigurieren der benannten Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-484"><xref:Microsoft.Extensions.Options.IPostConfigureOptions`1.PostConfigure*> is available to post-configure named options:</span></span>

```csharp
services.PostConfigure<MyOptions>("named_options_1", myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

<span data-ttu-id="3d8b5-485">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> zur Postkonfiguration aller Konfigurationsinstanzen:</span><span class="sxs-lookup"><span data-stu-id="3d8b5-485">Use <xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.PostConfigureAll*> to post-configure all configuration instances:</span></span>

```csharp
services.PostConfigureAll<MyOptions>(myOptions =>
{
    myOptions.Option1 = "post_configured_option1_value";
});
```

## <a name="accessing-options-during-startup"></a><span data-ttu-id="3d8b5-486">Zugreifen auf Optionen während des Starts</span><span class="sxs-lookup"><span data-stu-id="3d8b5-486">Accessing options during startup</span></span>

<span data-ttu-id="3d8b5-487"><xref:Microsoft.Extensions.Options.IOptions%601> und <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> können in `Startup.Configure` verwendet werden, da Dienste erstellt werden, bevor die `Configure`-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-487"><xref:Microsoft.Extensions.Options.IOptions%601> and <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> can be used in `Startup.Configure`, since services are built before the `Configure` method executes.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptionsMonitor<MyOptions> optionsAccessor)
{
    var option1 = optionsAccessor.CurrentValue.Option1;
}
```

<span data-ttu-id="3d8b5-488">Verwenden Sie <xref:Microsoft.Extensions.Options.IOptions%601> oder <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> nicht in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-488">Don't use <xref:Microsoft.Extensions.Options.IOptions%601> or <xref:Microsoft.Extensions.Options.IOptionsMonitor%601> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3d8b5-489">Es kann einen inkonsistenten Optionszustand geben. Dies liegt an der Reihenfolge der Dienstregistrierungen.</span><span class="sxs-lookup"><span data-stu-id="3d8b5-489">An inconsistent options state may exist due to the ordering of service registrations.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3d8b5-490">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3d8b5-490">Additional resources</span></span>

* <xref:fundamentals/configuration/index>
