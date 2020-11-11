---
title: Erkennen von Änderungen mit Änderungstoken in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Änderungstoken verwenden, um Änderungen nachzuverfolgen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/change-tokens
ms.openlocfilehash: f20d44c7767b284f727ce19a46224dae0cf6a5e1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053773"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="ff8b8-103">Erkennen von Änderungen mit Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff8b8-103">Detect changes with change tokens in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ff8b8-104">Ein *Änderungstoken* ist ein universeller Baustein auf niedriger Ebene, mit dem Änderungen nachverfolgt werden können.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-104">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="ff8b8-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff8b8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="ff8b8-106">IChangeToken-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="ff8b8-106">IChangeToken interface</span></span>

<span data-ttu-id="ff8b8-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> verbreitet Benachrichtigungen, wenn eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-107"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="ff8b8-108">`IChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-108">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="ff8b8-109">Das [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/)-NuGet-Paket wird implizit für die ASP.NET Core-Apps bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-109">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="ff8b8-110">`IChangeToken` verfügt über zwei Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-110">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="ff8b8-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> geben an, ob das Token proaktiv Rückrufe auslöst.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-111"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="ff8b8-112">Wenn `ActiveChangedCallbacks` auf `false` festgelegt ist, wird ein Rückruf nie aufgerufen, und die Anwendung muss `HasChanged` für Änderungen abrufen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-112">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="ff8b8-113">Es ist auch möglich, dass ein Token nie abgebrochen wird, wenn keine Änderungen auftreten oder der zugrunde liegenden Änderungslistener gelöscht oder deaktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-113">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="ff8b8-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> ruft einen Wert auf, der angibt, ob eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-114"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="ff8b8-115">Die `IChangeToken`-Schnittstelle verfügt über die Methode [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*). Diese registriert einen Rückruf, der bei einer Tokenänderung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-115">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="ff8b8-116">`HasChanged` muss festgelegt werden, bevor der Rückruf aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-116">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="ff8b8-117">ChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="ff8b8-117">ChangeToken class</span></span>

<span data-ttu-id="ff8b8-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> ist eine statische Klasse, die zur Verbreitung von Änderungsbenachrichtigungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-118"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="ff8b8-119">`ChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-119">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="ff8b8-120">Das [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/)-NuGet-Paket wird implizit für die ASP.NET Core-Apps bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-120">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="ff8b8-121">Die Methode [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registriert eine bei einer Tokenänderung aufzurufende `Action`:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-121">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="ff8b8-122">`Func<IChangeToken>` erzeugt das Token.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-122">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="ff8b8-123">`Action` wird aufgerufen, wenn das Token geändert wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-123">`Action` is called when the token changes.</span></span>

<span data-ttu-id="ff8b8-124">Die Überladung [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) akzeptiert einen zusätzlichen `TState`-Parameter, der an die Tokenconsumer-`Action` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-124">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="ff8b8-125"><xref:System.IDisposable> gibt `OnChange` zurück.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-125">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="ff8b8-126">Der Aufruf von <xref:System.IDisposable.Dispose*> beendet den Überwachungsvorgang des Tokens auf weitere Änderungen und gibt die Tokenressourcen frei.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-126">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="ff8b8-127">Beispielverwendungen von Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff8b8-127">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="ff8b8-128">Änderungstoken werden in wichtigen Bereichen von ASP.NET Core verwendet, um auf Objektänderungen zu überwachen:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-128">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="ff8b8-129">Für die Überwachung von Änderungen an Dateien erstellt die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*>-Methode von <xref:Microsoft.Extensions.FileProviders.IFileProvider> ein `IChangeToken` für die angegebenen Dateien oder einen Ordner, der überwacht werden soll.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-129">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="ff8b8-130">`IChangeToken`-Token können zu Cacheeinträgen hinzugefügt werden, um bei Änderungen Cacheentfernungen auszulösen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-130">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="ff8b8-131">Für `TOptions`-Änderungen umfasst die <xref:Microsoft.Extensions.Options.OptionsMonitor`1>-Standardimplementierung von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> eine Überladung, die eine oder mehrere <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>-Instanzen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-131">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="ff8b8-132">Jede Instanz gibt ein `IChangeToken` zurück, um den Rückruf einer Änderungsbenachrichtigung zur Nachverfolgung von Optionsänderungen zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-132">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="ff8b8-133">Überwachen von Konfigurationsänderungen</span><span class="sxs-lookup"><span data-stu-id="ff8b8-133">Monitor for configuration changes</span></span>

<span data-ttu-id="ff8b8-134">ASP.NET Core-Vorlagen verwenden standardmäßig [JSON-Konfigurationsdateien](xref:fundamentals/configuration/index#json-configuration-provider) ( *:::no-loc(appsettings.json):::* , *appsettings.Development.json* und *appsettings.Production.json* ), um Konfigurationseinstellungen der Anwendungen zu laden.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-134">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) ( *:::no-loc(appsettings.json):::* , *appsettings.Development.json* , and *appsettings.Production.json* ) to load app configuration settings.</span></span>

<span data-ttu-id="ff8b8-135">Diese Dateien werden mithilfe der [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*)-Erweiterungsmethode für <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> konfiguriert, die einen `reloadOnChange`-Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-135">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="ff8b8-136">`reloadOnChange` gibt an, ob Konfigurationen auf Dateiänderungen neu geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-136">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="ff8b8-137">Diese Einstellung wird in der <xref:Microsoft.Extensions.Hosting.Host>-Hilfsmethode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> verwendet:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-137">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile(":::no-loc(appsettings.json):::", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="ff8b8-138">Die dateibasierte Konfiguration wird durch <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> repräsentiert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-138">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="ff8b8-139">`FileConfigurationSource` verwendet <xref:Microsoft.Extensions.FileProviders.IFileProvider> zum Überwachen von Dateien.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-139">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="ff8b8-140">`IFileMonitor` wird standardmäßig von einem <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> bereitgestellt, der zum Überwachen auf Konfigurationsdateiänderungen <xref:System.IO.FileSystemWatcher> verwendet.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-140">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="ff8b8-141">Die Beispielanwendung veranschaulicht zwei Implementierungen für die Überwachung von Konfigurationsänderungen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-141">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="ff8b8-142">Wenn sich eine beliebige der *appsettings* -Dateien ändert, führen beide Dateiüberwachungsimplementierungen benutzerdefinierten Code aus – die Beispiel-App gibt eine Meldung an die Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-142">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="ff8b8-143">Der `FileSystemWatcher` einer Konfigurationsdatei kann mehrere Tokenrückrufe für eine einzelne Dateikonfigurationsänderung auslösen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-143">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="ff8b8-144">Um sicherzustellen, dass der benutzerdefinierte Code bei Auslösung mehrerer Tokenrückrufe nur einmal ausgeführt wird, überprüft die Beispielimplementierung Dateihashes.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-144">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="ff8b8-145">Das Beispiel verwendet SHA1-Dateihashing.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-145">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="ff8b8-146">Eine Wiederholung wird mit einem exponentiellen Backoff implementiert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-146">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="ff8b8-147">Die Wiederholung ist vorhanden, weil Dateisperren auftreten können, die vorübergehend das Berechnen eines neuen Hashs für eine der Dateien verhindern.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-147">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="ff8b8-148">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="ff8b8-148">*Utilities/Utilities.cs* :</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="ff8b8-149">Einfaches Starten von Änderungstoken</span><span class="sxs-lookup"><span data-stu-id="ff8b8-149">Simple startup change token</span></span>

<span data-ttu-id="ff8b8-150">Registrieren Sie einen `Action`-Rückruf eines Tokenconsumers für Änderungsbenachrichtigungen an das Token zum Neuladen der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-150">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="ff8b8-151">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-151">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="ff8b8-152">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-152">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="ff8b8-153">Der Rückruf ist die `InvokeChanged`-Methode:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-153">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="ff8b8-154">Der `state` des Rückrufs wird zur Übergabe an die `IWebHostEnvironment` verwendet. Dies ist hilfreich, um die richtige *appsettings* -Konfigurationsdatei anzugeben, die überwacht werden soll (in der Entwicklungsumgebung beispielsweise *appsettings.Development.json* ).</span><span class="sxs-lookup"><span data-stu-id="ff8b8-154">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="ff8b8-155">Dateihashes werden verwendet, um zu verhindern, dass die `WriteConsole`-Anweisung mehrere Male ausgeführt wird. Dies liegt an mehreren Tokenrückrufen, wenn die Konfigurationsdatei nur einmal geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-155">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="ff8b8-156">Dieses System wird so lange ausgeführt, wie die Anwendung ausgeführt wird. Es kann nicht vom Benutzer deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-156">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="ff8b8-157">Überwachen von Konfigurationsänderungen als Dienst</span><span class="sxs-lookup"><span data-stu-id="ff8b8-157">Monitor configuration changes as a service</span></span>

<span data-ttu-id="ff8b8-158">Das Beispiel implementiert:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-158">The sample implements:</span></span>

* <span data-ttu-id="ff8b8-159">Grundlegendes Überwachen des Starttokens.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-159">Basic startup token monitoring.</span></span>
* <span data-ttu-id="ff8b8-160">Überwachung als Dienst.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-160">Monitoring as a service.</span></span>
* <span data-ttu-id="ff8b8-161">Ein Mechanismus zum Aktivieren und Deaktivieren des Monitoring.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-161">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="ff8b8-162">In diesem Beispiel wird eine `IConfigurationMonitor`-Schnittstelle eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-162">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="ff8b8-163">*Extensions/ConfigurationMonitor.cs* :</span><span class="sxs-lookup"><span data-stu-id="ff8b8-163">*Extensions/ConfigurationMonitor.cs* :</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="ff8b8-164">Der Konstruktor der implementierten `ConfigurationMonitor`-Klasse registriert einen Rückruf für Änderungsbenachrichtigungen:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-164">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="ff8b8-165">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-165">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="ff8b8-166">`InvokeChanged` ist die Rückrufmethode.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-166">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="ff8b8-167">In dieser Instanz stellt `state` einen Verweis auf die `IConfigurationMonitor`-Instanz dar, die zum Zugriff auf den Überwachungsstatus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-167">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="ff8b8-168">Es werden zwei Eigenschaften verwendet:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-168">Two properties are used:</span></span>

* <span data-ttu-id="ff8b8-169">`MonitoringEnabled`: Diese Eigenschaft gibt an, ob der Rückruf den zugehörigen benutzerdefinierten Code ausführen soll.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-169">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="ff8b8-170">`CurrentState`: Diese Eigenschaft beschreibt den aktuellen Überwachungsstatus zur Verwendung in der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-170">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="ff8b8-171">Die `InvokeChanged`-Methode ist vergleichbar mit dem früheren Ansatz, außer dass sie:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-171">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="ff8b8-172">Den Code nicht ausführt, es sei denn, `MonitoringEnabled` ist `true`.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-172">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="ff8b8-173">Den aktuellen `state` in der zugehörigen `WriteConsole`-Ausgabe berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-173">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="ff8b8-174">Eine `ConfigurationMonitor`-Instanz wird in `Startup.ConfigureServices` als Dienst registriert:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-174">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ff8b8-175">Die Indexseite bietet das Benutzer Kontrolle über die Konfigurationsüberwachung.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-175">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="ff8b8-176">Die `IConfigurationMonitor`-Instanz wird in das `IndexModel` eingefügt.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-176">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="ff8b8-177">*Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ff8b8-177">*Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="ff8b8-178">Der Konfigurationsmonitor (`_monitor`) wird verwendet, um die Überwachung zu aktivieren oder zu deaktivieren und den aktuellen Status für Benutzeroberflächenfeedback festzulegen:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-178">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="ff8b8-179">Wenn `OnPostStartMonitoring` ausgelöst wird, wird die Überwachung aktiviert, und der aktuelle Status ist deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="ff8b8-180">Wenn `OnPostStopMonitoring` ausgelöst wird, wird Überwachung deaktiviert, und der Zustand zeigt, dass die Überwachung nicht ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="ff8b8-181">Die Überwachung wird über Schaltflächen in der Benutzeroberfläche aktiviert und deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-181">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="ff8b8-182">*Pages/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ff8b8-182">*Pages/Index.cshtml* :</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="ff8b8-183">Überwachen von zwischengespeicherten Dateiänderungen</span><span class="sxs-lookup"><span data-stu-id="ff8b8-183">Monitor cached file changes</span></span>

<span data-ttu-id="ff8b8-184">Dateiinhalt kann mithilfe von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> speicherintern zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-184">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="ff8b8-185">Zwischenspeicherung im Arbeitsspeicher wird im Thema [Cache in-memory (Zwischenspeicherung im Arbeitsspeicher)](xref:performance/caching/memory) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-185">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="ff8b8-186">Ohne zusätzliche Schritte, wie der unten beschriebenen Implementierung, werden *veraltete* Daten aus einem Zwischenspeicher zurückgegeben, wenn sich die Quelldaten ändern.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-186">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="ff8b8-187">Wird beispielsweise der Status einer zwischengespeicherten Quelldatei beim Erneuern eines [variablen Ablaufzeitraums](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) nicht berücksichtigt, führt dies zu veralteten Dateidaten im Cache.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-187">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="ff8b8-188">Jede Datenanforderung erneuert den variablen Ablaufzeitraum, aber die Datei wird nie neu in den Zwischenspeicher geladen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-188">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="ff8b8-189">Alle Anwendungsfeatures, die den zwischengespeicherten Inhalt der Datei verwenden, erhalten möglicherweise veraltete Inhalte.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-189">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="ff8b8-190">Durch die Verwendung von Änderungstoken in einem Szenario mit Dateizwischenspeicherung wird verhindert, dass veraltete Dateiinhalte im Zwischenspeicher auftreten.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-190">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="ff8b8-191">Die Beispielanwendung zeigt eine Implementierung des Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-191">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="ff8b8-192">Das Beispiel verwendet `GetFileContent`, um:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-192">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="ff8b8-193">Dateiinhalt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-193">Return file content.</span></span>
* <span data-ttu-id="ff8b8-194">Einen Wiederholungsalgorithmus mit exponentiellem Backoff zu implementieren, um Fälle abzudecken, in denen eine Dateisperre vorübergehend das Lesen einer Datei verhindert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-194">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="ff8b8-195">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="ff8b8-195">*Utilities/Utilities.cs* :</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="ff8b8-196">Ein `FileService` wird erstellt, um die zwischengespeicherten Dateisuchvorgänge zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-196">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="ff8b8-197">Der `GetFileContent`-Methodenaufruf des Diensts versucht, den Dateiinhalt aus dem speicherinternen Cache abzurufen und ihn an den Aufrufer zurückzugeben ( *Services/FileService.cs* ).</span><span class="sxs-lookup"><span data-stu-id="ff8b8-197">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller ( *Services/FileService.cs* ).</span></span>

<span data-ttu-id="ff8b8-198">Wenn mithilfe des Cacheschlüssels zwischengespeicherte Inhalte nicht gefunden werden, werden folgende Aktionen durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-198">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="ff8b8-199">Der Inhalt der Datei wird mit `GetFileContent` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-199">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="ff8b8-200">Ein Änderungstoken wird mithilfe von [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) aus dem Dateianbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-200">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="ff8b8-201">Wenn die Datei geändert wird, wird ein Rückruf des Tokens ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-201">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="ff8b8-202">Der Inhalt der Datei wird mit einem [variablen Ablaufzeitraum](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-202">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="ff8b8-203">Das Änderungstoken wird mit [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) angefügt, um den Cacheeintrag zu entfernen, wenn die sich Datei ändert, während sie zwischengespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-203">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="ff8b8-204">Im folgenden Beispiel werden Dateien im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App gespeichert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-204">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="ff8b8-205">`IWebHostEnvironment.ContentRootFileProvider` wird verwendet, um einen <xref:Microsoft.Extensions.FileProviders.IFileProvider> zu erhalten, der auf den `IWebHostEnvironment.ContentRootPath` der App verweist.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-205">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="ff8b8-206">Der `filePath` wird mit [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) abgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-206">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="ff8b8-207">Der `FileService` ist zusammen mit dem Speichercachedienst im Dienstcontainer registriert:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-207">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="ff8b8-208">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-208">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="ff8b8-209">Das Seitenmodell lädt mithilfe des Diensts den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-209">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="ff8b8-210">In der `OnGet`-Methode auf der Indexseite ( *Pages/Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="ff8b8-210">In the Index page's `OnGet` method ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="ff8b8-211">CompositeChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="ff8b8-211">CompositeChangeToken class</span></span>

<span data-ttu-id="ff8b8-212">Verwenden Sie für die Darstellung von einer oder mehreren `IChangeToken`-Instanzen in einem einzelnen Objekt die Klasse <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-212">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="ff8b8-213">`HasChanged` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `HasChanged`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-213">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="ff8b8-214">`ActiveChangeCallbacks` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `ActiveChangeCallbacks`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-214">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="ff8b8-215">Wenn mehrere Änderungsereignisse gleichzeitig auftreten, wird der Rückruf für die kombinierte Änderung einmal aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-215">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff8b8-216">Ein *Änderungstoken* ist ein universeller Baustein auf niedriger Ebene, mit dem Änderungen nachverfolgt werden können.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-216">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="ff8b8-217">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff8b8-217">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="ff8b8-218">IChangeToken-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="ff8b8-218">IChangeToken interface</span></span>

<span data-ttu-id="ff8b8-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> verbreitet Benachrichtigungen, wenn eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-219"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="ff8b8-220">`IChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-220">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="ff8b8-221">Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nicht verwenden, erstellen Sie einen Paketverweis auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="ff8b8-221">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="ff8b8-222">`IChangeToken` verfügt über zwei Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-222">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="ff8b8-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> geben an, ob das Token proaktiv Rückrufe auslöst.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-223"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="ff8b8-224">Wenn `ActiveChangedCallbacks` auf `false` festgelegt ist, wird ein Rückruf nie aufgerufen, und die Anwendung muss `HasChanged` für Änderungen abrufen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-224">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="ff8b8-225">Es ist auch möglich, dass ein Token nie abgebrochen wird, wenn keine Änderungen auftreten oder der zugrunde liegenden Änderungslistener gelöscht oder deaktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-225">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="ff8b8-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> ruft einen Wert auf, der angibt, ob eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-226"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="ff8b8-227">Die `IChangeToken`-Schnittstelle verfügt über die Methode [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*). Diese registriert einen Rückruf, der bei einer Tokenänderung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-227">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="ff8b8-228">`HasChanged` muss festgelegt werden, bevor der Rückruf aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-228">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="ff8b8-229">ChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="ff8b8-229">ChangeToken class</span></span>

<span data-ttu-id="ff8b8-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> ist eine statische Klasse, die zur Verbreitung von Änderungsbenachrichtigungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-230"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="ff8b8-231">`ChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-231">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="ff8b8-232">Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nicht verwenden, erstellen Sie einen Paketverweis auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="ff8b8-232">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="ff8b8-233">Die Methode [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registriert eine bei einer Tokenänderung aufzurufende `Action`:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-233">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="ff8b8-234">`Func<IChangeToken>` erzeugt das Token.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-234">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="ff8b8-235">`Action` wird aufgerufen, wenn das Token geändert wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-235">`Action` is called when the token changes.</span></span>

<span data-ttu-id="ff8b8-236">Die Überladung [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) akzeptiert einen zusätzlichen `TState`-Parameter, der an die Tokenconsumer-`Action` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-236">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="ff8b8-237"><xref:System.IDisposable> gibt `OnChange` zurück.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-237">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="ff8b8-238">Der Aufruf von <xref:System.IDisposable.Dispose*> beendet den Überwachungsvorgang des Tokens auf weitere Änderungen und gibt die Tokenressourcen frei.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-238">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="ff8b8-239">Beispielverwendungen von Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ff8b8-239">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="ff8b8-240">Änderungstoken werden in wichtigen Bereichen von ASP.NET Core verwendet, um auf Objektänderungen zu überwachen:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-240">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="ff8b8-241">Für die Überwachung von Änderungen an Dateien erstellt die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*>-Methode von <xref:Microsoft.Extensions.FileProviders.IFileProvider> ein `IChangeToken` für die angegebenen Dateien oder einen Ordner, der überwacht werden soll.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-241">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="ff8b8-242">`IChangeToken`-Token können zu Cacheeinträgen hinzugefügt werden, um bei Änderungen Cacheentfernungen auszulösen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-242">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="ff8b8-243">Für `TOptions`-Änderungen umfasst die <xref:Microsoft.Extensions.Options.OptionsMonitor`1>-Standardimplementierung von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> eine Überladung, die eine oder mehrere <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>-Instanzen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-243">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="ff8b8-244">Jede Instanz gibt ein `IChangeToken` zurück, um den Rückruf einer Änderungsbenachrichtigung zur Nachverfolgung von Optionsänderungen zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-244">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="ff8b8-245">Überwachen von Konfigurationsänderungen</span><span class="sxs-lookup"><span data-stu-id="ff8b8-245">Monitor for configuration changes</span></span>

<span data-ttu-id="ff8b8-246">ASP.NET Core-Vorlagen verwenden standardmäßig [JSON-Konfigurationsdateien](xref:fundamentals/configuration/index#json-configuration-provider) ( *:::no-loc(appsettings.json):::* , *appsettings.Development.json* und *appsettings.Production.json* ), um Konfigurationseinstellungen der Anwendungen zu laden.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-246">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) ( *:::no-loc(appsettings.json):::* , *appsettings.Development.json* , and *appsettings.Production.json* ) to load app configuration settings.</span></span>

<span data-ttu-id="ff8b8-247">Diese Dateien werden mithilfe der [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*)-Erweiterungsmethode für <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> konfiguriert, die einen `reloadOnChange`-Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-247">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="ff8b8-248">`reloadOnChange` gibt an, ob Konfigurationen auf Dateiänderungen neu geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-248">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="ff8b8-249">Diese Einstellung wird in der <xref:Microsoft.AspNetCore.WebHost>-Hilfsmethode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> verwendet:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-249">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile(":::no-loc(appsettings.json):::", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="ff8b8-250">Die dateibasierte Konfiguration wird durch <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> repräsentiert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-250">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="ff8b8-251">`FileConfigurationSource` verwendet <xref:Microsoft.Extensions.FileProviders.IFileProvider> zum Überwachen von Dateien.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-251">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="ff8b8-252">`IFileMonitor` wird standardmäßig von einem <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> bereitgestellt, der zum Überwachen auf Konfigurationsdateiänderungen <xref:System.IO.FileSystemWatcher> verwendet.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-252">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="ff8b8-253">Die Beispielanwendung veranschaulicht zwei Implementierungen für die Überwachung von Konfigurationsänderungen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-253">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="ff8b8-254">Wenn sich eine beliebige der *appsettings* -Dateien ändert, führen beide Dateiüberwachungsimplementierungen benutzerdefinierten Code aus – die Beispiel-App gibt eine Meldung an die Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-254">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="ff8b8-255">Der `FileSystemWatcher` einer Konfigurationsdatei kann mehrere Tokenrückrufe für eine einzelne Dateikonfigurationsänderung auslösen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-255">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="ff8b8-256">Um sicherzustellen, dass der benutzerdefinierte Code bei Auslösung mehrerer Tokenrückrufe nur einmal ausgeführt wird, überprüft die Beispielimplementierung Dateihashes.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-256">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="ff8b8-257">Das Beispiel verwendet SHA1-Dateihashing.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-257">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="ff8b8-258">Eine Wiederholung wird mit einem exponentiellen Backoff implementiert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-258">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="ff8b8-259">Die Wiederholung ist vorhanden, weil Dateisperren auftreten können, die vorübergehend das Berechnen eines neuen Hashs für eine der Dateien verhindern.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-259">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="ff8b8-260">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="ff8b8-260">*Utilities/Utilities.cs* :</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="ff8b8-261">Einfaches Starten von Änderungstoken</span><span class="sxs-lookup"><span data-stu-id="ff8b8-261">Simple startup change token</span></span>

<span data-ttu-id="ff8b8-262">Registrieren Sie einen `Action`-Rückruf eines Tokenconsumers für Änderungsbenachrichtigungen an das Token zum Neuladen der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-262">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="ff8b8-263">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-263">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="ff8b8-264">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-264">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="ff8b8-265">Der Rückruf ist die `InvokeChanged`-Methode:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-265">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="ff8b8-266">Der `state` des Rückrufs wird zur Übergabe an die `IHostingEnvironment` verwendet. Dies ist hilfreich, um die richtige *appsettings* -Konfigurationsdatei anzugeben, die überwacht werden soll (in der Entwicklungsumgebung beispielsweise *appsettings.Development.json* ).</span><span class="sxs-lookup"><span data-stu-id="ff8b8-266">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="ff8b8-267">Dateihashes werden verwendet, um zu verhindern, dass die `WriteConsole`-Anweisung mehrere Male ausgeführt wird. Dies liegt an mehreren Tokenrückrufen, wenn die Konfigurationsdatei nur einmal geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-267">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="ff8b8-268">Dieses System wird so lange ausgeführt, wie die Anwendung ausgeführt wird. Es kann nicht vom Benutzer deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-268">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="ff8b8-269">Überwachen von Konfigurationsänderungen als Dienst</span><span class="sxs-lookup"><span data-stu-id="ff8b8-269">Monitor configuration changes as a service</span></span>

<span data-ttu-id="ff8b8-270">Das Beispiel implementiert:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-270">The sample implements:</span></span>

* <span data-ttu-id="ff8b8-271">Grundlegendes Überwachen des Starttokens.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-271">Basic startup token monitoring.</span></span>
* <span data-ttu-id="ff8b8-272">Überwachung als Dienst.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-272">Monitoring as a service.</span></span>
* <span data-ttu-id="ff8b8-273">Ein Mechanismus zum Aktivieren und Deaktivieren des Monitoring.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-273">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="ff8b8-274">In diesem Beispiel wird eine `IConfigurationMonitor`-Schnittstelle eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-274">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="ff8b8-275">*Extensions/ConfigurationMonitor.cs* :</span><span class="sxs-lookup"><span data-stu-id="ff8b8-275">*Extensions/ConfigurationMonitor.cs* :</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="ff8b8-276">Der Konstruktor der implementierten `ConfigurationMonitor`-Klasse registriert einen Rückruf für Änderungsbenachrichtigungen:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-276">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="ff8b8-277">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-277">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="ff8b8-278">`InvokeChanged` ist die Rückrufmethode.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-278">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="ff8b8-279">In dieser Instanz stellt `state` einen Verweis auf die `IConfigurationMonitor`-Instanz dar, die zum Zugriff auf den Überwachungsstatus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-279">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="ff8b8-280">Es werden zwei Eigenschaften verwendet:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-280">Two properties are used:</span></span>

* <span data-ttu-id="ff8b8-281">`MonitoringEnabled`: Diese Eigenschaft gibt an, ob der Rückruf den zugehörigen benutzerdefinierten Code ausführen soll.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-281">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="ff8b8-282">`CurrentState`: Diese Eigenschaft beschreibt den aktuellen Überwachungsstatus zur Verwendung in der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-282">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="ff8b8-283">Die `InvokeChanged`-Methode ist vergleichbar mit dem früheren Ansatz, außer dass sie:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-283">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="ff8b8-284">Den Code nicht ausführt, es sei denn, `MonitoringEnabled` ist `true`.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-284">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="ff8b8-285">Den aktuellen `state` in der zugehörigen `WriteConsole`-Ausgabe berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-285">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="ff8b8-286">Eine `ConfigurationMonitor`-Instanz wird in `Startup.ConfigureServices` als Dienst registriert:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-286">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="ff8b8-287">Die Indexseite bietet das Benutzer Kontrolle über die Konfigurationsüberwachung.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-287">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="ff8b8-288">Die `IConfigurationMonitor`-Instanz wird in das `IndexModel` eingefügt.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-288">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="ff8b8-289">*Pages/Index.cshtml.cs* :</span><span class="sxs-lookup"><span data-stu-id="ff8b8-289">*Pages/Index.cshtml.cs* :</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="ff8b8-290">Der Konfigurationsmonitor (`_monitor`) wird verwendet, um die Überwachung zu aktivieren oder zu deaktivieren und den aktuellen Status für Benutzeroberflächenfeedback festzulegen:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-290">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="ff8b8-291">Wenn `OnPostStartMonitoring` ausgelöst wird, wird die Überwachung aktiviert, und der aktuelle Status ist deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-291">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="ff8b8-292">Wenn `OnPostStopMonitoring` ausgelöst wird, wird Überwachung deaktiviert, und der Zustand zeigt, dass die Überwachung nicht ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-292">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="ff8b8-293">Die Überwachung wird über Schaltflächen in der Benutzeroberfläche aktiviert und deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-293">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="ff8b8-294">*Pages/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="ff8b8-294">*Pages/Index.cshtml* :</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="ff8b8-295">Überwachen von zwischengespeicherten Dateiänderungen</span><span class="sxs-lookup"><span data-stu-id="ff8b8-295">Monitor cached file changes</span></span>

<span data-ttu-id="ff8b8-296">Dateiinhalt kann mithilfe von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> speicherintern zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-296">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="ff8b8-297">Zwischenspeicherung im Arbeitsspeicher wird im Thema [Cache in-memory (Zwischenspeicherung im Arbeitsspeicher)](xref:performance/caching/memory) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-297">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="ff8b8-298">Ohne zusätzliche Schritte, wie der unten beschriebenen Implementierung, werden *veraltete* Daten aus einem Zwischenspeicher zurückgegeben, wenn sich die Quelldaten ändern.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-298">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="ff8b8-299">Wird beispielsweise der Status einer zwischengespeicherten Quelldatei beim Erneuern eines [variablen Ablaufzeitraums](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) nicht berücksichtigt, führt dies zu veralteten Dateidaten im Cache.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-299">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="ff8b8-300">Jede Datenanforderung erneuert den variablen Ablaufzeitraum, aber die Datei wird nie neu in den Zwischenspeicher geladen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-300">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="ff8b8-301">Alle Anwendungsfeatures, die den zwischengespeicherten Inhalt der Datei verwenden, erhalten möglicherweise veraltete Inhalte.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-301">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="ff8b8-302">Durch die Verwendung von Änderungstoken in einem Szenario mit Dateizwischenspeicherung wird verhindert, dass veraltete Dateiinhalte im Zwischenspeicher auftreten.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-302">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="ff8b8-303">Die Beispielanwendung zeigt eine Implementierung des Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-303">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="ff8b8-304">Das Beispiel verwendet `GetFileContent`, um:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-304">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="ff8b8-305">Dateiinhalt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-305">Return file content.</span></span>
* <span data-ttu-id="ff8b8-306">Einen Wiederholungsalgorithmus mit exponentiellem Backoff zu implementieren, um Fälle abzudecken, in denen eine Dateisperre vorübergehend das Lesen einer Datei verhindert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-306">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="ff8b8-307">*Utilities/Utilities.cs* :</span><span class="sxs-lookup"><span data-stu-id="ff8b8-307">*Utilities/Utilities.cs* :</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="ff8b8-308">Ein `FileService` wird erstellt, um die zwischengespeicherten Dateisuchvorgänge zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-308">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="ff8b8-309">Der `GetFileContent`-Methodenaufruf des Diensts versucht, den Dateiinhalt aus dem speicherinternen Cache abzurufen und ihn an den Aufrufer zurückzugeben ( *Services/FileService.cs* ).</span><span class="sxs-lookup"><span data-stu-id="ff8b8-309">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller ( *Services/FileService.cs* ).</span></span>

<span data-ttu-id="ff8b8-310">Wenn mithilfe des Cacheschlüssels zwischengespeicherte Inhalte nicht gefunden werden, werden folgende Aktionen durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-310">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="ff8b8-311">Der Inhalt der Datei wird mit `GetFileContent` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-311">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="ff8b8-312">Ein Änderungstoken wird mithilfe von [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) aus dem Dateianbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-312">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="ff8b8-313">Wenn die Datei geändert wird, wird ein Rückruf des Tokens ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-313">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="ff8b8-314">Der Inhalt der Datei wird mit einem [variablen Ablaufzeitraum](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-314">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="ff8b8-315">Das Änderungstoken wird mit [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) angefügt, um den Cacheeintrag zu entfernen, wenn die sich Datei ändert, während sie zwischengespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-315">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="ff8b8-316">Im folgenden Beispiel werden Dateien im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App gespeichert.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-316">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span> <span data-ttu-id="ff8b8-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) wird verwendet, um einen <xref:Microsoft.Extensions.FileProviders.IFileProvider> abzurufen, der auf den <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> der App zeigt.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-317">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="ff8b8-318">Der `filePath` wird mit [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) abgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-318">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="ff8b8-319">Der `FileService` ist zusammen mit dem Speichercachedienst im Dienstcontainer registriert:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-319">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="ff8b8-320">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ff8b8-320">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="ff8b8-321">Das Seitenmodell lädt mithilfe des Diensts den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-321">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="ff8b8-322">In der `OnGet`-Methode auf der Indexseite ( *Pages/Index.cshtml.cs* ):</span><span class="sxs-lookup"><span data-stu-id="ff8b8-322">In the Index page's `OnGet` method ( *Pages/Index.cshtml.cs* ):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="ff8b8-323">CompositeChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="ff8b8-323">CompositeChangeToken class</span></span>

<span data-ttu-id="ff8b8-324">Verwenden Sie für die Darstellung von einer oder mehreren `IChangeToken`-Instanzen in einem einzelnen Objekt die Klasse <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-324">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="ff8b8-325">`HasChanged` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `HasChanged`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-325">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="ff8b8-326">`ActiveChangeCallbacks` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `ActiveChangeCallbacks`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-326">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="ff8b8-327">Wenn mehrere Änderungsereignisse gleichzeitig auftreten, wird der Rückruf für die kombinierte Änderung einmal aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff8b8-327">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ff8b8-328">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ff8b8-328">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
