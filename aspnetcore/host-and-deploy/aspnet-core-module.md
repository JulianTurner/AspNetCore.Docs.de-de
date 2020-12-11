---
title: ASP.NET Core-Modul
author: rick-anderson
description: Hier erfahren Sie, wie Sie das ASP.NET Core-Modul so konfigurieren, dass es ASP.NET Core-Apps mit den IIS hosten kann.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/aspnet-core-module
ms.openlocfilehash: d0e6c0c31890c58aaca936fc6f1e92cb9a1ab456
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901235"
---
# <a name="aspnet-core-module"></a><span data-ttu-id="9339b-103">ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="9339b-103">ASP.NET Core Module</span></span>

<span data-ttu-id="9339b-104">Von [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti) und [ Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="9339b-104">By [Tom Dykstra](https://github.com/tdykstra), [Rick Strahl](https://github.com/RickStrahl), [Chris Ross](https://github.com/Tratcher), [Rick Anderson](https://twitter.com/RickAndMSFT), [Sourabh Shirhatti](https://twitter.com/sshirhatti), and [Justin Kotalik](https://github.com/jkotalik)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9339b-105">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, was ermöglicht, dass ASP.NET Core-Anwendungen mit den IIS arbeiten können.</span><span class="sxs-lookup"><span data-stu-id="9339b-105">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline, allowing ASP.NET Core applications to work with IIS.</span></span> <span data-ttu-id="9339b-106">Für das Ausführen von ASP.NET Core-Apps mit den IIS gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="9339b-106">Run ASP.NET Core apps with IIS by either:</span></span> 

* <span data-ttu-id="9339b-107">Hosten einer ASP.NET Core-App innerhalb des IIS-Workerprozesses (`w3wp.exe`), was als [In-Process-Hostingmodell](xref:host-and-deploy/iis/in-process-hosting) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-107">Hosting an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](xref:host-and-deploy/iis/in-process-hosting).</span></span>
* <span data-ttu-id="9339b-108">Weiterleiten von Webanforderungen an eine Back-End-ASP.NET Core-App, die den Kestrel-Server ausführt, was als [Out-of-Process-Hostingmodell](xref:host-and-deploy/iis/out-of-process-hosting) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-108">Forwarding web requests to a backend ASP.NET Core app running the Kestrel server, called the [out-of-process hosting model](xref:host-and-deploy/iis/out-of-process-hosting).</span></span>

<span data-ttu-id="9339b-109">Für jedes der Hostingmodelle müssen Kompromisse eingegangen werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-109">There are trade-offs between each of the hosting models.</span></span> <span data-ttu-id="9339b-110">Standardmäßig wird das In-Process-Hostingmodell verwendet, da die Leistung und Diagnose hier besser sind.</span><span class="sxs-lookup"><span data-stu-id="9339b-110">By default, the in-process hosting model is used due to better performance and diagnostics.</span></span>

## <a name="install-aspnet-core-module"></a><span data-ttu-id="9339b-111">Installieren des ASP.NET Core-Moduls</span><span class="sxs-lookup"><span data-stu-id="9339b-111">Install ASP.NET Core Module</span></span>

<span data-ttu-id="9339b-112">Laden Sie den Installer über folgenden Link herunter:</span><span class="sxs-lookup"><span data-stu-id="9339b-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="9339b-113">Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)</span><span class="sxs-lookup"><span data-stu-id="9339b-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

<span data-ttu-id="9339b-114">Ausführlichere Anweisungen zum Installieren des ASP.NET Core-Moduls oder einer anderen Version des Moduls finden Sie unter [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="9339b-114">For more details instructions on how to install the ASP.NET Core Module, or installing different versions of the module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/hosting-bundle).</span></span>

<span data-ttu-id="9339b-115">Ein Tutorial zum Veröffentlichen einer ASP.NET Core-App auf einem IIS-Server finden Sie unter <xref:tutorials/publish-to-iis>.</span><span class="sxs-lookup"><span data-stu-id="9339b-115">For a tutorial experience on publishing an ASP.NET Core app to an IIS server, see <xref:tutorials/publish-to-iis>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="9339b-116">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um eine dieser Aktionen auszuführen:</span><span class="sxs-lookup"><span data-stu-id="9339b-116">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="9339b-117">Hosten einer ASP.NET Core-App innerhalb des IIS-Arbeitsprozesses (`w3wp.exe`), was als [In-Process-Hostingmodell](#in-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-117">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="9339b-118">Weiterleiten von Webanforderungen an eine Back-End-ASP.NET Core-App, die den [Kestrel-Server](xref:fundamentals/servers/kestrel) ausführt, was als [Out-of-Process-Hostingmodell](#out-of-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-118">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="9339b-119">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="9339b-119">Supported Windows versions:</span></span>

* <span data-ttu-id="9339b-120">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="9339b-120">Windows 7 or later</span></span>
* <span data-ttu-id="9339b-121">Windows Server 2012 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="9339b-121">Windows Server 2012 R2 or later</span></span>

<span data-ttu-id="9339b-122">Beim In-Process-Hosting verwendet das Modul eine In-Process-Server-Implementierung für IIS, die als IIS-HTTP-Server (`IISHttpServer`) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-122">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="9339b-123">Beim Out-of-Process-Hosting funktioniert das Modul nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9339b-123">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="9339b-124">Dieses Modul funktioniert nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="9339b-124">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="9339b-125">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="9339b-125">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="9339b-126">In-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="9339b-126">In-process hosting model</span></span>

<span data-ttu-id="9339b-127">ASP.NET Core-Apps verwenden standardmäßig das In-Process-Hostingmodell.</span><span class="sxs-lookup"><span data-stu-id="9339b-127">ASP.NET Core apps default to the in-process hosting model.</span></span>

<span data-ttu-id="9339b-128">Die folgenden Merkmale treffen für In-Process-Hosting zu:</span><span class="sxs-lookup"><span data-stu-id="9339b-128">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="9339b-129">Der IIS-HTTP-Server (`IISHttpServer`) wird anstelle des [Kestrel](xref:fundamentals/servers/kestrel)-Servers verwendet.</span><span class="sxs-lookup"><span data-stu-id="9339b-129">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="9339b-130">Für In-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="9339b-130">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="9339b-131">Registrieren des `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="9339b-131">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="9339b-132">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-132">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="9339b-133">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="9339b-133">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="9339b-134">Das [RequestTimeout-Attribut](#attributes-of-the-aspnetcore-element) trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="9339b-134">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="9339b-135">Das gemeinsame Verwenden eines Anwendungspools durch mehrere Apps wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9339b-135">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="9339b-136">Verwenden Sie einen Anwendungspool pro App.</span><span class="sxs-lookup"><span data-stu-id="9339b-136">Use one app pool per app.</span></span>

* <span data-ttu-id="9339b-137">Wenn [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) verwendet oder eine [`app_offline.htm`-Datei manuell in der Bereitstellung platziert wird](xref:host-and-deploy/iis/index#locked-deployment-files), wird die App möglicherweise nicht sofort beendet, wenn eine offene Verbindung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="9339b-137">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [`app_offline.htm` file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="9339b-138">Beispielsweise kann eine WebSocket-Verbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="9339b-138">For example, a WebSocket connection may delay app shut down.</span></span>

* <span data-ttu-id="9339b-139">Die Architektur (Bitbreite) der App und der installierten Runtime (x64 oder x86) müssen mit der Architektur des Anwendungspools übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="9339b-139">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="9339b-140">Verbindungstrennungen von Clients werden erkannt.</span><span class="sxs-lookup"><span data-stu-id="9339b-140">Client disconnects are detected.</span></span> <span data-ttu-id="9339b-141">Das [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*)-Abbruchtoken wird abgebrochen, wenn der Client die Verbindung trennt.</span><span class="sxs-lookup"><span data-stu-id="9339b-141">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="9339b-142">In ASP.NET Core 2.2.1 oder früher gibt <xref:System.IO.Directory.GetCurrentDirectory*> anstelle des Anwendungsverzeichnisses das Workerverzeichnis des Prozesses zurück, der von den IIS gestartet wurde (z.B. `C:\Windows\System32\inetsrv` für `w3wp.exe`).</span><span class="sxs-lookup"><span data-stu-id="9339b-142">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, `C:\Windows\System32\inetsrv` for `w3wp.exe`).</span></span>

  <span data-ttu-id="9339b-143">Den Beispielcode zum Festlegen des aktuellen App-Verzeichnisses finden Sie unter [`CurrentDirectoryHelpers`-Klasse](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="9339b-143">For sample code that sets the app's current directory, see the [`CurrentDirectoryHelpers` class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/3.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="9339b-144">Rufen Sie die `SetCurrentDirectory`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="9339b-144">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="9339b-145">Nachfolgende Aufrufe von <xref:System.IO.Directory.GetCurrentDirectory*> stellen das App-Verzeichnis bereit.</span><span class="sxs-lookup"><span data-stu-id="9339b-145">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="9339b-146">Beim In-Process-Hosting wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="9339b-146">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="9339b-147">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="9339b-147">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="9339b-148">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> auf, um Authentifizierungsdienste hinzuzufügen, wenn Ansprüche mit einer <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung transformiert werden:</span><span class="sxs-lookup"><span data-stu-id="9339b-148">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```
  
  * <span data-ttu-id="9339b-149">[Bereitstellungen von Webpaketen (Einzeldateien)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9339b-149">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="9339b-150">Out-of-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="9339b-150">Out-of-process hosting model</span></span>

<span data-ttu-id="9339b-151">Um eine App für Out-of-Process-Hosting zu konfigurieren, legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft in der Projektdatei ( `.csproj`) auf `OutOfProcess` fest:</span><span class="sxs-lookup"><span data-stu-id="9339b-151">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="9339b-152">Das In-Process-Hosting wird mithilfe von `InProcess` (Standardwert) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9339b-152">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="9339b-153">Beim Wert von `<AspNetCoreHostingModel>` wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.</span><span class="sxs-lookup"><span data-stu-id="9339b-153">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="9339b-154">Der [Kestrel](xref:fundamentals/servers/kestrel)-Server wird anstelle des IIS-HTTP-Servers (`IISHttpServer`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="9339b-154">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="9339b-155">Bei Out-of-Process ruft [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> auf, um Folgendes zu tun:</span><span class="sxs-lookup"><span data-stu-id="9339b-155">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="9339b-156">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-156">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="9339b-157">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="9339b-157">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="9339b-158">Änderungen am Hostmodell</span><span class="sxs-lookup"><span data-stu-id="9339b-158">Hosting model changes</span></span>

<span data-ttu-id="9339b-159">Wenn die Einstellung `hostingModel` in der Datei `web.config` geändert wird (wird im Abschnitt [Konfiguration mit `web.config`](#configuration-with-webconfig) erläutert), verwendet das Modul den Workerprozess für die IIS noch mal.</span><span class="sxs-lookup"><span data-stu-id="9339b-159">If the `hostingModel` setting is changed in the `web.config` file (explained in the [Configuration with `web.config`](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="9339b-160">Bei IIS Express verwendet das Modul den Arbeitsprozess nicht erneut, sondern löst stattdessen ein ordnungsgemäßes Herunterfahren des aktuellen IIS Express-Prozesses aus.</span><span class="sxs-lookup"><span data-stu-id="9339b-160">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="9339b-161">Mit der nächsten Anforderung an die App wird ein neuer IIS Express-Prozess erzeugt.</span><span class="sxs-lookup"><span data-stu-id="9339b-161">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="9339b-162">Prozessname</span><span class="sxs-lookup"><span data-stu-id="9339b-162">Process name</span></span>

<span data-ttu-id="9339b-163">`Process.GetCurrentProcess().ProcessName` meldet `w3wp`/`iisexpress` (In-Process) oder `dotnet` (Out-of-Process).</span><span class="sxs-lookup"><span data-stu-id="9339b-163">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="9339b-164">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="9339b-164">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="9339b-165">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="9339b-165">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="9339b-166">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="9339b-166">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="9339b-167">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="9339b-167">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="9339b-168">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="9339b-168">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="9339b-169">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="9339b-169">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="9339b-170">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="9339b-170">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="9339b-171">Anweisungen zum Installieren des ASP.NET Core-Moduls finden Sie unter [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="9339b-171">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="9339b-172">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="9339b-172">Configuration with web.config</span></span>

<span data-ttu-id="9339b-173">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="9339b-173">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="9339b-174">Die folgende `web.config`-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="9339b-174">The following `web.config` file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="9339b-175">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="9339b-175">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="9339b-176">Die <xref:System.Configuration.SectionInformation.InheritInChildApplications*>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="9339b-176">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="9339b-177">Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.</span><span class="sxs-lookup"><span data-stu-id="9339b-177">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="9339b-178">Der Pfad speichert stdout-Protokolle zum Ordner `LogFiles`, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-178">The path saves stdout logs to the `LogFiles` folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="9339b-179">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="9339b-179">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="9339b-180">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="9339b-180">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="9339b-181">Attribut</span><span class="sxs-lookup"><span data-stu-id="9339b-181">Attribute</span></span> | <span data-ttu-id="9339b-182">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="9339b-182">Description</span></span> | <span data-ttu-id="9339b-183">Standard</span><span class="sxs-lookup"><span data-stu-id="9339b-183">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="9339b-184">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-184">Optional string attribute.</span></span></p><p><span data-ttu-id="9339b-185">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="9339b-185">Arguments to the executable specified in **processPath**.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="9339b-186">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-186">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9339b-187">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="9339b-187">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="9339b-188">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-188">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9339b-189">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an `%ASPNETCORE_PORT%` als Header `'MS-ASPNETCORE-WINAUTHTOKEN'` pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-189">If true, the token is forwarded to the child process listening on `%ASPNETCORE_PORT%` as a header `'MS-ASPNETCORE-WINAUTHTOKEN'` per request.</span></span> <span data-ttu-id="9339b-190">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="9339b-190">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="9339b-191">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-191">Optional string attribute.</span></span></p><p><span data-ttu-id="9339b-192">Gibt das Hostingmodell als In-Process (`InProcess`/`inprocess`) oder Out-of-Process (`OutOfProcess`/`outofprocess`) an.</span><span class="sxs-lookup"><span data-stu-id="9339b-192">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p><span data-ttu-id="9339b-193">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-193">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-194">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="9339b-194">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="9339b-195">&dagger;Für In-Process-Hosting ist dieser Wert auf `1` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="9339b-195">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="9339b-196">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="9339b-196">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="9339b-197">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="9339b-197">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="9339b-198">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="9339b-198">Default: `1`</span></span><br><span data-ttu-id="9339b-199">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="9339b-199">Min: `1`</span></span><br><span data-ttu-id="9339b-200">Max.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="9339b-200">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="9339b-201">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-201">Required string attribute.</span></span></p><p><span data-ttu-id="9339b-202">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-202">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="9339b-203">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9339b-203">Relative paths are supported.</span></span> <span data-ttu-id="9339b-204">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="9339b-204">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="9339b-205">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-205">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-206">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="9339b-206">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="9339b-207">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="9339b-207">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="9339b-208">Bei In-Process-Hosting nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9339b-208">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="9339b-209">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="9339b-209">Default: `10`</span></span><br><span data-ttu-id="9339b-210">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="9339b-210">Min: `0`</span></span><br><span data-ttu-id="9339b-211">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="9339b-211">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="9339b-212">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-212">Optional timespan attribute.</span></span></p><p><span data-ttu-id="9339b-213">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-213">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="9339b-214">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="9339b-214">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="9339b-215">Trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="9339b-215">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="9339b-216">Bei In-Process-Hosting wartet das Modul darauf, dass die App die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="9339b-216">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="9339b-217">Gültige Werte für Minuten- und Sekundensegmente der Zeichenfolge befinden sich im Bereich 0–59.</span><span class="sxs-lookup"><span data-stu-id="9339b-217">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="9339b-218">Die Verwendung von **60** im Wert für Minuten- oder Sekundenergebnisse führt zu einem *500 – Interner Serverfehler*.</span><span class="sxs-lookup"><span data-stu-id="9339b-218">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="9339b-219">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="9339b-219">Default: `00:02:00`</span></span><br><span data-ttu-id="9339b-220">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="9339b-220">Min: `00:00:00`</span></span><br><span data-ttu-id="9339b-221">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="9339b-221">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="9339b-222">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-222">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-223">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-223">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="9339b-224">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="9339b-224">Default: `10`</span></span><br><span data-ttu-id="9339b-225">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="9339b-225">Min: `0`</span></span><br><span data-ttu-id="9339b-226">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="9339b-226">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="9339b-227">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-227">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-228">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-228">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="9339b-229">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="9339b-229">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="9339b-230">Bei *In-Process*-Hosting: Der Prozess wird **nicht** neu gestartet und verwendet **nicht** die Einstellung **rapidFailsPerMinute**.</span><span class="sxs-lookup"><span data-stu-id="9339b-230">When hosting *in-process*: The process is **not** restarted and does **not** use the **rapidFailsPerMinute** setting.</span></span></p><p><span data-ttu-id="9339b-231">Bei *Out-of-Process*-Hosting: Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="9339b-231">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="9339b-232">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="9339b-232">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="9339b-233">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="9339b-233">Default: `120`</span></span><br><span data-ttu-id="9339b-234">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="9339b-234">Min: `0`</span></span><br><span data-ttu-id="9339b-235">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="9339b-235">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="9339b-236">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-236">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9339b-237">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="9339b-237">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="9339b-238">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-238">Optional string attribute.</span></span></p><p><span data-ttu-id="9339b-239">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="9339b-239">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="9339b-240">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="9339b-240">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="9339b-241">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="9339b-241">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="9339b-242">Ordner, die im Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-242">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="9339b-243">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung ( `.log`) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9339b-243">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="9339b-244">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als `stdout_20180205194132_1934.log` im Ordner `logs` gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="9339b-244">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a><span data-ttu-id="9339b-245">Festlegen von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="9339b-245">Set environment variables</span></span>

<span data-ttu-id="9339b-246">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-246">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="9339b-247">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="9339b-247">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="9339b-248">In diesem Abschnitt festgelegte Umgebungsvariablen haben Vorrang vor Systemumgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="9339b-248">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="9339b-249">Im folgenden Beispiel werden zwei Umgebungsvariablen in `web.config` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9339b-249">The following example sets two environment variables in `web.config`.</span></span> <span data-ttu-id="9339b-250">`ASPNETCORE_ENVIRONMENT` konfiguriert die Umgebung der App als `Development`.</span><span class="sxs-lookup"><span data-stu-id="9339b-250">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="9339b-251">Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei `web.config`, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="9339b-251">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="9339b-252">`CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.</span><span class="sxs-lookup"><span data-stu-id="9339b-252">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="9339b-253">Eine Alternative zum direkten Festlegen der Umgebung in `web.config` ist das Einschließen der `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) oder eine Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="9339b-253">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="9339b-254">Dieser Ansatz legt die Umgebung in `web.config` fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="9339b-254">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="9339b-255">Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="9339b-255">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## `app_offline.htm`

<span data-ttu-id="9339b-256">Wenn eine Datei mit dem Namen `app_offline.htm` im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="9339b-256">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="9339b-257">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurden, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="9339b-257">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="9339b-258">Wenn die Datei `app_offline.htm` vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der `app_offline.htm`-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="9339b-258">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="9339b-259">Wenn die Datei `app_offline.htm` entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="9339b-259">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="9339b-260">Beim Verwenden des Out-of-Process-Hostingmodells wird die App möglicherweise nicht sofort heruntergefahren, wenn eine offene Verbindung besteht.</span><span class="sxs-lookup"><span data-stu-id="9339b-260">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="9339b-261">Beispielsweise kann eine WebSocket-Verbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="9339b-261">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="9339b-262">Startfehler-Seite</span><span class="sxs-lookup"><span data-stu-id="9339b-262">Start-up error page</span></span>

<span data-ttu-id="9339b-263">Sowohl In-Process- als auch Out-of-Process-Hosting erzeugen benutzerdefinierte Fehlerseiten, wenn die App nicht gestartet werden kann.</span><span class="sxs-lookup"><span data-stu-id="9339b-263">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="9339b-264">Wenn das ASP.NET Core-Modul weder den In-Process- noch den Out-of-Process-Anforderungshandler finden kann, wird die Statuscodeseite *500.0: Fehler beim Laden des In-Process-/Out-Of-Process-Handlers* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9339b-264">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="9339b-265">Wenn das ASP.NET Core-Modul beim In-Process-Hosting die App nicht starten kann, wird die Statuscodeseite *500.30: Fehler beim Starten* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9339b-265">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="9339b-266">Wenn das ASP.NET Core-Modul beim Out-of-Process-Hosting den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9339b-266">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="9339b-267">Um diese Seite zu unterdrücken und zur standardmäßigen IIS-5xx-Statuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="9339b-267">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="9339b-268">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="9339b-268">For more information on configuring custom error messages, see [HTTP Errors `<httpErrors>`](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="9339b-269">Protokollerstellung und Weiterleitung</span><span class="sxs-lookup"><span data-stu-id="9339b-269">Log creation and redirection</span></span>

<span data-ttu-id="9339b-270">Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-270">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="9339b-271">Ordner, die im `stdoutLogFile`-Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-271">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="9339b-272">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="9339b-272">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="9339b-273">Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet.</span><span class="sxs-lookup"><span data-stu-id="9339b-273">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="9339b-274">Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.</span><span class="sxs-lookup"><span data-stu-id="9339b-274">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="9339b-275">Die Verwendung des stdout-Protokolls wird nur für die Problembehandlung bei App-Startproblemen beim Hosten in IIS oder beim Verwenden von [Support zum Zeitpunkt der Entwicklung für IIS in Visual Studio](xref:host-and-deploy/iis/development-time-iis-support) empfohlen, nicht aber für das lokale Debuggen und das Ausführen der App mit IIS Express.</span><span class="sxs-lookup"><span data-stu-id="9339b-275">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="9339b-276">Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken.</span><span class="sxs-lookup"><span data-stu-id="9339b-276">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="9339b-277">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="9339b-277">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="9339b-278">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="9339b-278">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="9339b-279">Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-279">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="9339b-280">Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung ( `.log`) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel `stdout`), getrennt durch Unterstriche, angehängt werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-280">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="9339b-281">Wenn der `stdoutLogFile`-Pfad mit `stdout` endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen `stdout_20180205194132_1934.log`.</span><span class="sxs-lookup"><span data-stu-id="9339b-281">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="9339b-282">Wenn `stdoutLogEnabled` „false“ ist, werden Fehler beim App-Start erfasst und in das Ereignisprotokoll mit bis zu 30 KB ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="9339b-282">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="9339b-283">Nach dem Start werden alle zusätzlichen Protokolle verworfen.</span><span class="sxs-lookup"><span data-stu-id="9339b-283">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="9339b-284">Das folgende Beispielelement `aspNetCore` konfiguriert die stdout-Protokollierung im relativen `.\log\`-Pfad.</span><span class="sxs-lookup"><span data-stu-id="9339b-284">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="9339b-285">Vergewissern Sie sich, dass die Identität des AppPool-Benutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.</span><span class="sxs-lookup"><span data-stu-id="9339b-285">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="9339b-286">Beim Veröffentlichen einer App für die Azure App Service-Bereitstellung legt das Web SDK den Wert `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` fest.</span><span class="sxs-lookup"><span data-stu-id="9339b-286">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="9339b-287">Die Umgebungsvariable `%home` ist für Apps vordefiniert, die von Azure App Service gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-287">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="9339b-288">Informationen zum Erstellen von Protokollierungsfilterregeln finden Sie in den Abschnitten [Konfiguration](xref:fundamentals/logging/index#log-filtering) und [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) der Dokumentation zur ASP.NET Core-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="9339b-288">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="9339b-289">Weitere Informationen zu Pfadformaten finden Sie unter [Formate von Dateipfaden unter Windows-Systemen](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="9339b-289">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="9339b-290">Erweiterte Diagnoseprotokolle</span><span class="sxs-lookup"><span data-stu-id="9339b-290">Enhanced diagnostic logs</span></span>

<span data-ttu-id="9339b-291">Das ASP.NET Core-Modul kann so konfiguriert werden, dass es erweiterte Diagnoseprotokolle bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="9339b-291">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="9339b-292">Fügen Sie das Element `<handlerSettings>` zum Element `<aspNetCore>` in `web.config` hinzu.</span><span class="sxs-lookup"><span data-stu-id="9339b-292">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="9339b-293">Wenn `debugLevel` auf `TRACE` festgelegt wird, werden genauere Diagnoseinformationen zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="9339b-293">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="9339b-294">Ordner, die im Pfad angegeben werden (`logs` im vorherigen Beispiel), werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-294">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="9339b-295">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, in den die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\{APP POOL NAME}`, um die Schreibberechtigung bereitzustellen, wenn der Platzhalter `{APP POOL NAME}` der App-Poolname ist).</span><span class="sxs-lookup"><span data-stu-id="9339b-295">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}`, where the placeholder `{APP POOL NAME}` is the app pool name, to provide write permission).</span></span>

<span data-ttu-id="9339b-296">`debugLevel`-Werte können sowohl die Ebene als auch den Speicherort enthalten.</span><span class="sxs-lookup"><span data-stu-id="9339b-296">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="9339b-297">Ebenen (von der geringsten zur höchsten Genauigkeit):</span><span class="sxs-lookup"><span data-stu-id="9339b-297">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="9339b-298">ERROR</span><span class="sxs-lookup"><span data-stu-id="9339b-298">ERROR</span></span>
* <span data-ttu-id="9339b-299">WARNING</span><span class="sxs-lookup"><span data-stu-id="9339b-299">WARNING</span></span>
* <span data-ttu-id="9339b-300">INFO</span><span class="sxs-lookup"><span data-stu-id="9339b-300">INFO</span></span>
* <span data-ttu-id="9339b-301">TRACE</span><span class="sxs-lookup"><span data-stu-id="9339b-301">TRACE</span></span>

<span data-ttu-id="9339b-302">Speicherorte (mehrere Speicherorte sind zulässig):</span><span class="sxs-lookup"><span data-stu-id="9339b-302">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="9339b-303">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="9339b-303">CONSOLE</span></span>
* <span data-ttu-id="9339b-304">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="9339b-304">EVENTLOG</span></span>
* <span data-ttu-id="9339b-305">DATEI</span><span class="sxs-lookup"><span data-stu-id="9339b-305">FILE</span></span>

<span data-ttu-id="9339b-306">Die Handlereinstellungen können auch über Umgebungsvariablen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="9339b-306">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="9339b-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Dies ist der Pfad zur Debugprotokolldatei.</span><span class="sxs-lookup"><span data-stu-id="9339b-307">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="9339b-308">(Standard: `aspnetcore-debug.log`)</span><span class="sxs-lookup"><span data-stu-id="9339b-308">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="9339b-309">`ASPNETCORE_MODULE_DEBUG`: Dies ist die Einstellung der Debugebene.</span><span class="sxs-lookup"><span data-stu-id="9339b-309">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="9339b-310">Lassen Sie die Debugprotokollierung **nicht** länger als erforderlich für die Bereitstellung aktiviert, wenn Sie einen Fehler beheben.</span><span class="sxs-lookup"><span data-stu-id="9339b-310">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="9339b-311">Die Größe des Protokolls ist nicht beschränkt.</span><span class="sxs-lookup"><span data-stu-id="9339b-311">The size of the log isn't limited.</span></span> <span data-ttu-id="9339b-312">Wenn die Debugprotokollierung aktiviert bleibt, kann der verfügbare Speicherplatz aufgebraucht werden, und der Server- oder App-Dienst können abstürzen.</span><span class="sxs-lookup"><span data-stu-id="9339b-312">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="9339b-313">[Konfiguration mit der Datei „web.config“](#configuration-with-webconfig) enthält ein Beispiel für das `aspNetCore`-Element in der Datei `web.config`.</span><span class="sxs-lookup"><span data-stu-id="9339b-313">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="9339b-314">Ändern der Stapelgröße</span><span class="sxs-lookup"><span data-stu-id="9339b-314">Modify the stack size</span></span>

<span data-ttu-id="9339b-315">*Gilt nur bei Verwendung des In-Process-Hostingmodells.*</span><span class="sxs-lookup"><span data-stu-id="9339b-315">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="9339b-316">Konfigurieren Sie die Größe des verwalteten Stapels mithilfe der `stackSize`-Einstellung in Byte in `web.config`.</span><span class="sxs-lookup"><span data-stu-id="9339b-316">Configure the managed stack size using the `stackSize` setting in bytes in `web.config`.</span></span> <span data-ttu-id="9339b-317">Die Standardgröße beträgt 1.048.576 Byte (1 MB).</span><span class="sxs-lookup"><span data-stu-id="9339b-317">The default size is 1,048,576 bytes (1 MB).</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="9339b-318">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="9339b-318">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="9339b-319">*Gilt nur für Out-of-Process-Hosting.*</span><span class="sxs-lookup"><span data-stu-id="9339b-319">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="9339b-320">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="9339b-320">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="9339b-321">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="9339b-321">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="9339b-322">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="9339b-322">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="9339b-323">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9339b-323">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="9339b-324">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9339b-324">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="9339b-325">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="9339b-325">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="9339b-326">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="9339b-326">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="9339b-327">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-327">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="9339b-328">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="9339b-328">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="9339b-329">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="9339b-329">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="9339b-330">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des **TrustedInstaller**-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9339b-330">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="9339b-331">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der `applicationHost.config`-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="9339b-331">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="9339b-332">Wenn eine ISS-Freigabekonfiguration auf demselben Computer verwendet wird wie die ISS-Installation, führen Sie das Installationsprogramm des ASP.NET -Core-Hosting-Pakets mit auf `1` festgelegtem Parameter `OPT_NO_SHARED_CONFIG_CHECK` aus:</span><span class="sxs-lookup"><span data-stu-id="9339b-332">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="9339b-333">Wenn sich der Pfad zur freigegebenen Konfiguration nicht auf demselben Computer wie die ISS-Installation befindet, befolgen Sie die folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="9339b-333">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="9339b-334">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="9339b-334">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="9339b-335">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="9339b-335">Run the installer.</span></span>
1. <span data-ttu-id="9339b-336">Exportieren Sie die aktualisierte Datei `applicationHost.config` auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="9339b-336">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="9339b-337">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="9339b-337">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="9339b-338">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="9339b-338">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="9339b-339">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="9339b-339">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="9339b-340">Navigieren Sie auf dem Hostsystem zu `%windir%\System32\inetsrv`.</span><span class="sxs-lookup"><span data-stu-id="9339b-340">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="9339b-341">Suchen Sie die Datei `aspnetcore.dll`.</span><span class="sxs-lookup"><span data-stu-id="9339b-341">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="9339b-342">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="9339b-342">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="9339b-343">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="9339b-343">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="9339b-344">Die Installationsprotokolle des Hostingbundles für das Modul finden Sie unter `C:\Users\%UserName%\AppData\Local\Temp`.</span><span class="sxs-lookup"><span data-stu-id="9339b-344">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="9339b-345">Die Datei heißt `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span><span class="sxs-lookup"><span data-stu-id="9339b-345">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="9339b-346">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="9339b-346">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="9339b-347">Modul</span><span class="sxs-lookup"><span data-stu-id="9339b-347">Module</span></span>

<span data-ttu-id="9339b-348">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="9339b-348">**IIS (x86/amd64):**</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="9339b-349">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="9339b-349">**IIS Express (x86/amd64):**</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="9339b-350">Schema</span><span class="sxs-lookup"><span data-stu-id="9339b-350">Schema</span></span>

<span data-ttu-id="9339b-351">**IIS**</span><span class="sxs-lookup"><span data-stu-id="9339b-351">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="9339b-352">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="9339b-352">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="9339b-353">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="9339b-353">Configuration</span></span>

<span data-ttu-id="9339b-354">**IIS**</span><span class="sxs-lookup"><span data-stu-id="9339b-354">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="9339b-355">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="9339b-355">**IIS Express**</span></span>

* <span data-ttu-id="9339b-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="9339b-356">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="9339b-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="9339b-357">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="9339b-358">Den Speicherort dieser Dateien finden Sie, indem Sie `aspnetcore` in der Datei `applicationHost.config` suchen.</span><span class="sxs-lookup"><span data-stu-id="9339b-358">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9339b-359">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um eine dieser Aktionen auszuführen:</span><span class="sxs-lookup"><span data-stu-id="9339b-359">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to either:</span></span>

* <span data-ttu-id="9339b-360">Hosten einer ASP.NET Core-App innerhalb des IIS-Arbeitsprozesses (`w3wp.exe`), was als [In-Process-Hostingmodell](#in-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-360">Host an ASP.NET Core app inside of the IIS worker process (`w3wp.exe`), called the [in-process hosting model](#in-process-hosting-model).</span></span>
* <span data-ttu-id="9339b-361">Weiterleiten von Webanforderungen an eine Back-End-ASP.NET Core-App, die den [Kestrel-Server](xref:fundamentals/servers/kestrel) ausführt, was als [Out-of-Process-Hostingmodell](#out-of-process-hosting-model) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-361">Forward web requests to a backend ASP.NET Core app running the [Kestrel server](xref:fundamentals/servers/kestrel), called the [out-of-process hosting model](#out-of-process-hosting-model).</span></span>

<span data-ttu-id="9339b-362">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="9339b-362">Supported Windows versions:</span></span>

* <span data-ttu-id="9339b-363">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="9339b-363">Windows 7 or later</span></span>
* <span data-ttu-id="9339b-364">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="9339b-364">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="9339b-365">Beim In-Process-Hosting verwendet das Modul eine In-Process-Server-Implementierung für IIS, die als IIS-HTTP-Server (`IISHttpServer`) bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-365">When hosting in-process, the module uses an in-process server implementation for IIS, called IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="9339b-366">Beim Out-of-Process-Hosting funktioniert das Modul nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9339b-366">When hosting out-of-process, the module only works with Kestrel.</span></span> <span data-ttu-id="9339b-367">Dieses Modul funktioniert nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="9339b-367">The module doesn't function with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

## <a name="hosting-models"></a><span data-ttu-id="9339b-368">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="9339b-368">Hosting models</span></span>

### <a name="in-process-hosting-model"></a><span data-ttu-id="9339b-369">In-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="9339b-369">In-process hosting model</span></span>

<span data-ttu-id="9339b-370">Um eine App für In-Process-Hosting zu konfigurieren, fügen Sie der Projektdatei der App die Eigenschaft `<AspNetCoreHostingModel>` mit dem Wert `InProcess` hinzu (Out-of-Process-Hosting wird mit `OutOfProcess` festgelegt):</span><span class="sxs-lookup"><span data-stu-id="9339b-370">To configure an app for in-process hosting, add the `<AspNetCoreHostingModel>` property to the app's project file with a value of `InProcess` (out-of-process hosting is set with `OutOfProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="9339b-371">Das In-Process-Hostingmodell wird nicht für ASP.NET Core-Apps unterstützt, die auf .NET Framework abzielen.</span><span class="sxs-lookup"><span data-stu-id="9339b-371">The in-process hosting model isn't supported for ASP.NET Core apps that target the .NET Framework.</span></span>

<span data-ttu-id="9339b-372">Beim Wert von `<AspNetCoreHostingModel>` wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.</span><span class="sxs-lookup"><span data-stu-id="9339b-372">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="9339b-373">Ist die `<AspNetCoreHostingModel>`-Eigenschaft nicht in der Datei vorhanden, ist `OutOfProcess` der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="9339b-373">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>

<span data-ttu-id="9339b-374">Die folgenden Merkmale treffen für In-Process-Hosting zu:</span><span class="sxs-lookup"><span data-stu-id="9339b-374">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="9339b-375">Der IIS-HTTP-Server (`IISHttpServer`) wird anstelle des [Kestrel](xref:fundamentals/servers/kestrel)-Servers verwendet.</span><span class="sxs-lookup"><span data-stu-id="9339b-375">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="9339b-376">Für In-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="9339b-376">For in-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS*> to:</span></span>

  * <span data-ttu-id="9339b-377">Registrieren des `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="9339b-377">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="9339b-378">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-378">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="9339b-379">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="9339b-379">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="9339b-380">Das [RequestTimeout-Attribut](#attributes-of-the-aspnetcore-element) trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="9339b-380">The [requestTimeout attribute](#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="9339b-381">Das gemeinsame Verwenden eines Anwendungspools durch mehrere Apps wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9339b-381">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="9339b-382">Verwenden Sie einen Anwendungspool pro App.</span><span class="sxs-lookup"><span data-stu-id="9339b-382">Use one app pool per app.</span></span>

* <span data-ttu-id="9339b-383">Wenn [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) verwendet oder eine [app_offline.htm-Datei manuell in der Bereitstellung platziert wird](xref:host-and-deploy/iis/index#locked-deployment-files), wird die App möglicherweise nicht sofort beendet, wenn eine offene Verbindung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="9339b-383">When using [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) or manually placing an [app_offline.htm file in the deployment](xref:host-and-deploy/iis/index#locked-deployment-files), the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="9339b-384">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="9339b-384">For example, a websocket connection may delay app shut down.</span></span>

* <span data-ttu-id="9339b-385">Die Architektur (Bitbreite) der App und der installierten Runtime (x64 oder x86) müssen mit der Architektur des Anwendungspools übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="9339b-385">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span>

* <span data-ttu-id="9339b-386">Verbindungstrennungen von Clients werden erkannt.</span><span class="sxs-lookup"><span data-stu-id="9339b-386">Client disconnects are detected.</span></span> <span data-ttu-id="9339b-387">Das Abbruchtoken [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) wird abgebrochen, wenn die Verbindung mit dem Client getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-387">The [HttpContext.RequestAborted](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted*) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="9339b-388">In ASP.NET Core 2.2.1 oder früher gibt <xref:System.IO.Directory.GetCurrentDirectory*> anstelle des Anwendungsverzeichnisses das Workerverzeichnis des Prozesses zurück, der von den IIS gestartet wurde (z.B. *C:\Windows\System32\inetsrv* für *w3wp.exe*).</span><span class="sxs-lookup"><span data-stu-id="9339b-388">In ASP.NET Core 2.2.1 or earlier, <xref:System.IO.Directory.GetCurrentDirectory*> returns the worker directory of the process started by IIS rather than the app's directory (for example, *C:\Windows\System32\inetsrv* for *w3wp.exe*).</span></span>

  <span data-ttu-id="9339b-389">Den Beispielcode zum Festlegen des aktuellen App-Verzeichnisses finden Sie in der Klasse [CurrentDirectoryHelpers](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span><span class="sxs-lookup"><span data-stu-id="9339b-389">For sample code that sets the app's current directory, see the [CurrentDirectoryHelpers class](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/aspnet-core-module/samples_snapshot/2.x/CurrentDirectoryHelpers.cs).</span></span> <span data-ttu-id="9339b-390">Rufen Sie die `SetCurrentDirectory`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="9339b-390">Call the `SetCurrentDirectory` method.</span></span> <span data-ttu-id="9339b-391">Nachfolgende Aufrufe von <xref:System.IO.Directory.GetCurrentDirectory*> stellen das App-Verzeichnis bereit.</span><span class="sxs-lookup"><span data-stu-id="9339b-391">Subsequent calls to <xref:System.IO.Directory.GetCurrentDirectory*> provide the app's directory.</span></span>

* <span data-ttu-id="9339b-392">Beim In-Process-Hosting wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="9339b-392">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="9339b-393">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="9339b-393">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="9339b-394">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> auf, um Authentifizierungsdienste hinzuzufügen, wenn Ansprüche mit einer <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung transformiert werden:</span><span class="sxs-lookup"><span data-stu-id="9339b-394">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> to add authentication services:</span></span>

  ```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddTransient<IClaimsTransformation, ClaimsTransformer>();
      services.AddAuthentication(IISServerDefaults.AuthenticationScheme);
  }

  public void Configure(IApplicationBuilder app)
  {
      app.UseAuthentication();
  }
  ```

### <a name="out-of-process-hosting-model"></a><span data-ttu-id="9339b-395">Out-of-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="9339b-395">Out-of-process hosting model</span></span>

<span data-ttu-id="9339b-396">Um eine App für Out-of-Process-Hosting zu konfigurieren, konfigurieren Sie die Projektdatei auf eine der folgenden Weisen:</span><span class="sxs-lookup"><span data-stu-id="9339b-396">To configure an app for out-of-process hosting, use either of the following approaches in the project file:</span></span>

* <span data-ttu-id="9339b-397">Geben Sie die `<AspNetCoreHostingModel>`-Eigenschaft nicht an.</span><span class="sxs-lookup"><span data-stu-id="9339b-397">Don't specify the `<AspNetCoreHostingModel>` property.</span></span> <span data-ttu-id="9339b-398">Ist die `<AspNetCoreHostingModel>`-Eigenschaft nicht in der Datei vorhanden, ist `OutOfProcess` der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="9339b-398">If the `<AspNetCoreHostingModel>` property isn't present in the file, the default value is `OutOfProcess`.</span></span>
* <span data-ttu-id="9339b-399">Legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft auf `OutOfProcess` fest (In-Process-Hosting wird mit `InProcess` festgelegt):</span><span class="sxs-lookup"><span data-stu-id="9339b-399">Set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` (in-process hosting is set with `InProcess`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="9339b-400">Beim Wert wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.</span><span class="sxs-lookup"><span data-stu-id="9339b-400">The value is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="9339b-401">Der [Kestrel](xref:fundamentals/servers/kestrel)-Server wird anstelle des IIS-HTTP-Servers (`IISHttpServer`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="9339b-401">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="9339b-402">Für Out-of-Process ruft [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host)<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> auf zu:</span><span class="sxs-lookup"><span data-stu-id="9339b-402">For out-of-process, [CreateDefaultBuilder](xref:fundamentals/host/web-host#set-up-a-host) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> to:</span></span>

* <span data-ttu-id="9339b-403">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-403">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="9339b-404">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="9339b-404">Configure the host to capture startup errors.</span></span>

### <a name="hosting-model-changes"></a><span data-ttu-id="9339b-405">Änderungen am Hostmodell</span><span class="sxs-lookup"><span data-stu-id="9339b-405">Hosting model changes</span></span>

<span data-ttu-id="9339b-406">Wenn die Einstellung `hostingModel` in der Datei *web.config* geändert wird (im Abschnitt [Konfiguration mit web.config](#configuration-with-webconfig) erläutert), verwendet das Modul den Arbeitsprozess von IIS erneut.</span><span class="sxs-lookup"><span data-stu-id="9339b-406">If the `hostingModel` setting is changed in the *web.config* file (explained in the [Configuration with web.config](#configuration-with-webconfig) section), the module recycles the worker process for IIS.</span></span>

<span data-ttu-id="9339b-407">Bei IIS Express verwendet das Modul den Arbeitsprozess nicht erneut, sondern löst stattdessen ein ordnungsgemäßes Herunterfahren des aktuellen IIS Express-Prozesses aus.</span><span class="sxs-lookup"><span data-stu-id="9339b-407">For IIS Express, the module doesn't recycle the worker process but instead triggers a graceful shutdown of the current IIS Express process.</span></span> <span data-ttu-id="9339b-408">Mit der nächsten Anforderung an die App wird ein neuer IIS Express-Prozess erzeugt.</span><span class="sxs-lookup"><span data-stu-id="9339b-408">The next request to the app spawns a new IIS Express process.</span></span>

### <a name="process-name"></a><span data-ttu-id="9339b-409">Prozessname</span><span class="sxs-lookup"><span data-stu-id="9339b-409">Process name</span></span>

<span data-ttu-id="9339b-410">`Process.GetCurrentProcess().ProcessName` meldet `w3wp`/`iisexpress` (In-Process) oder `dotnet` (Out-of-Process).</span><span class="sxs-lookup"><span data-stu-id="9339b-410">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="9339b-411">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="9339b-411">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="9339b-412">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="9339b-412">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="9339b-413">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="9339b-413">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="9339b-414">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="9339b-414">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="9339b-415">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="9339b-415">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="9339b-416">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="9339b-416">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="9339b-417">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="9339b-417">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="9339b-418">Anweisungen zum Installieren des ASP.NET Core-Moduls finden Sie unter [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="9339b-418">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="9339b-419">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="9339b-419">Configuration with web.config</span></span>

<span data-ttu-id="9339b-420">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="9339b-420">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="9339b-421">Die folgende *web.config*-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="9339b-421">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="9339b-422">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="9339b-422">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="9339b-423">Die <xref:System.Configuration.SectionInformation.InheritInChildApplications*>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="9339b-423">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the app.</span></span>

<span data-ttu-id="9339b-424">Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.</span><span class="sxs-lookup"><span data-stu-id="9339b-424">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="9339b-425">Der Pfad speichert stdout-Protokolle zum Ordner *LogFiles*, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-425">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="9339b-426">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="9339b-426">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="9339b-427">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="9339b-427">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="9339b-428">Attribut</span><span class="sxs-lookup"><span data-stu-id="9339b-428">Attribute</span></span> | <span data-ttu-id="9339b-429">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="9339b-429">Description</span></span> | <span data-ttu-id="9339b-430">Standard</span><span class="sxs-lookup"><span data-stu-id="9339b-430">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="9339b-431">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-431">Optional string attribute.</span></span></p><p><span data-ttu-id="9339b-432">Argumente zur ausführbaren Datei, die in `processPath` angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="9339b-432">Arguments to the executable specified in `processPath`.</span></span></p> | |
| `disableStartUpErrorPage` | <p><span data-ttu-id="9339b-433">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-433">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9339b-434">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="9339b-434">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="9339b-435">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-435">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9339b-436">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-436">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="9339b-437">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="9339b-437">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `hostingModel` | <p><span data-ttu-id="9339b-438">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-438">Optional string attribute.</span></span></p><p><span data-ttu-id="9339b-439">Gibt das Hostingmodell als In-Process (`InProcess`/`inprocess`) oder Out-of-Process (`OutOfProcess`/`outofprocess`) an.</span><span class="sxs-lookup"><span data-stu-id="9339b-439">Specifies the hosting model as in-process (`InProcess`/`inprocess`) or out-of-process (`OutOfProcess`/`outofprocess`).</span></span></p> | `OutOfProcess`<br>`outofprocess` |
| `processesPerApplication` | <p><span data-ttu-id="9339b-440">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-440">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-441">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung `processPath` an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="9339b-441">Specifies the number of instances of the process specified in the `processPath` setting that can be spun up per app.</span></span></p><p><span data-ttu-id="9339b-442">&dagger;Für In-Process-Hosting ist dieser Wert auf `1` beschränkt.</span><span class="sxs-lookup"><span data-stu-id="9339b-442">&dagger;For in-process hosting, the value is limited to `1`.</span></span></p><p><span data-ttu-id="9339b-443">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="9339b-443">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="9339b-444">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="9339b-444">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="9339b-445">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="9339b-445">Default: `1`</span></span><br><span data-ttu-id="9339b-446">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="9339b-446">Min: `1`</span></span><br><span data-ttu-id="9339b-447">Max.: `100`&dagger;</span><span class="sxs-lookup"><span data-stu-id="9339b-447">Max: `100`&dagger;</span></span> |
| `processPath` | <p><span data-ttu-id="9339b-448">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-448">Required string attribute.</span></span></p><p><span data-ttu-id="9339b-449">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-449">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="9339b-450">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9339b-450">Relative paths are supported.</span></span> <span data-ttu-id="9339b-451">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="9339b-451">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="9339b-452">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-452">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-453">Gibt an, wie viele Abstürze des in `processPath` angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="9339b-453">Specifies the number of times the process specified in `processPath` is allowed to crash per minute.</span></span> <span data-ttu-id="9339b-454">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="9339b-454">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p><p><span data-ttu-id="9339b-455">Bei In-Process-Hosting nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9339b-455">Not supported with in-process hosting.</span></span></p> | <span data-ttu-id="9339b-456">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="9339b-456">Default: `10`</span></span><br><span data-ttu-id="9339b-457">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="9339b-457">Min: `0`</span></span><br><span data-ttu-id="9339b-458">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="9339b-458">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="9339b-459">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-459">Optional timespan attribute.</span></span></p><p><span data-ttu-id="9339b-460">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-460">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="9339b-461">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="9339b-461">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p><p><span data-ttu-id="9339b-462">Trifft auf In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="9339b-462">Doesn't apply to in-process hosting.</span></span> <span data-ttu-id="9339b-463">Bei In-Process-Hosting wartet das Modul darauf, dass die App die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="9339b-463">For in-process hosting, the module waits for the app to process the request.</span></span></p><p><span data-ttu-id="9339b-464">Gültige Werte für Minuten- und Sekundensegmente der Zeichenfolge befinden sich im Bereich 0–59.</span><span class="sxs-lookup"><span data-stu-id="9339b-464">Valid values for minutes and seconds segments of the string are in the range 0-59.</span></span> <span data-ttu-id="9339b-465">Die Verwendung von **60** im Wert für Minuten- oder Sekundenergebnisse führt zu einem *500 – Interner Serverfehler*.</span><span class="sxs-lookup"><span data-stu-id="9339b-465">Use of **60** in the value for minutes or seconds results in a *500 - Internal Server Error*.</span></span></p> | <span data-ttu-id="9339b-466">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="9339b-466">Default: `00:02:00`</span></span><br><span data-ttu-id="9339b-467">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="9339b-467">Min: `00:00:00`</span></span><br><span data-ttu-id="9339b-468">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="9339b-468">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="9339b-469">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-469">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-470">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei `app_offline.htm` erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-470">Duration in seconds that the module waits for the executable to gracefully shutdown when the `app_offline.htm` file is detected.</span></span></p> | <span data-ttu-id="9339b-471">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="9339b-471">Default: `10`</span></span><br><span data-ttu-id="9339b-472">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="9339b-472">Min: `0`</span></span><br><span data-ttu-id="9339b-473">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="9339b-473">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="9339b-474">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-474">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-475">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-475">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="9339b-476">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="9339b-476">If this time limit is exceeded, the module kills the process.</span></span></p><p><span data-ttu-id="9339b-477">Bei *In-Process*-Hosting: Der Prozess wird **nicht** neu gestartet und verwendet **nicht** die Einstellung `rapidFailsPerMinute`.</span><span class="sxs-lookup"><span data-stu-id="9339b-477">When hosting *in-process*: The process is **not** restarted and does **not** use the `rapidFailsPerMinute` setting.</span></span></p><p><span data-ttu-id="9339b-478">Bei *Out-of-Process*-Hosting: Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann `rapidFailsPerMinute`-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="9339b-478">When hosting *out-of-process*: The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start `rapidFailsPerMinute` number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="9339b-479">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="9339b-479">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="9339b-480">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="9339b-480">Default: `120`</span></span><br><span data-ttu-id="9339b-481">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="9339b-481">Min: `0`</span></span><br><span data-ttu-id="9339b-482">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="9339b-482">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="9339b-483">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-483">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9339b-484">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in `processPath` angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="9339b-484">If true, **stdout** and **stderr** for the process specified in `processPath` are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="9339b-485">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-485">Optional string attribute.</span></span></p><p><span data-ttu-id="9339b-486">Gibt den relativen oder absoluten Pfad an, für den `stdout` und `stderr` aus dem in `processPath` angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="9339b-486">Specifies the relative or absolute file path for which `stdout` and `stderr` from the process specified in `processPath` are logged.</span></span> <span data-ttu-id="9339b-487">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="9339b-487">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="9339b-488">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="9339b-488">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="9339b-489">Ordner, die im Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-489">Any folders provided in the path are created by the module when the log file is created.</span></span> <span data-ttu-id="9339b-490">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung ( `.log`) dem letzten Segment des Pfads `stdoutLogFile` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9339b-490">Using underscore delimiters, a timestamp, process ID, and file extension (`.log`) are added to the last segment of the `stdoutLogFile` path.</span></span> <span data-ttu-id="9339b-491">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als `stdout_20180205194132_1934.log` im Ordner `logs` gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="9339b-491">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as `stdout_20180205194132_1934.log` in the `logs` folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="9339b-492">Festlegen von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="9339b-492">Setting environment variables</span></span>

<span data-ttu-id="9339b-493">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-493">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="9339b-494">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="9339b-494">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span> <span data-ttu-id="9339b-495">In diesem Abschnitt festgelegte Umgebungsvariablen haben Vorrang vor Systemumgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="9339b-495">Environment variables set in this section take precedence over system environment variables.</span></span>

<span data-ttu-id="9339b-496">Im folgenden Beispiel werden zwei Umgebungsvariablen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9339b-496">The following example sets two environment variables.</span></span> <span data-ttu-id="9339b-497">`ASPNETCORE_ENVIRONMENT` konfiguriert die Umgebung der App als `Development`.</span><span class="sxs-lookup"><span data-stu-id="9339b-497">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="9339b-498">Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei `web.config`, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="9339b-498">A developer may temporarily set this value in the `web.config` file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="9339b-499">`CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.</span><span class="sxs-lookup"><span data-stu-id="9339b-499">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> <span data-ttu-id="9339b-500">Eine Alternative zum direkten Festlegen der Umgebung in `web.config` ist das Einbeziehen der `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) oder eine Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="9339b-500">An alternative to setting the environment directly in `web.config` is to include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="9339b-501">Dieser Ansatz legt die Umgebung in `web.config` fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="9339b-501">This approach sets the environment in `web.config` when the project is published:</span></span>
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> <span data-ttu-id="9339b-502">Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="9339b-502">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="9339b-503">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="9339b-503">app_offline.htm</span></span>

<span data-ttu-id="9339b-504">Wenn eine Datei mit dem Namen `app_offline.htm` im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="9339b-504">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="9339b-505">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurden, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="9339b-505">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="9339b-506">Wenn die Datei `app_offline.htm` vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der `app_offline.htm`-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="9339b-506">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="9339b-507">Wenn die Datei `app_offline.htm` entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="9339b-507">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="9339b-508">Beim Verwenden des Out-of-Process-Hostingmodells wird die App möglicherweise nicht sofort heruntergefahren, wenn eine offene Verbindung besteht.</span><span class="sxs-lookup"><span data-stu-id="9339b-508">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="9339b-509">Beispielsweise kann eine Websocketverbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="9339b-509">For example, a websocket connection may delay app shut down.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="9339b-510">Startfehler-Seite</span><span class="sxs-lookup"><span data-stu-id="9339b-510">Start-up error page</span></span>

<span data-ttu-id="9339b-511">Sowohl In-Process- als auch Out-of-Process-Hosting erzeugen benutzerdefinierte Fehlerseiten, wenn die App nicht gestartet werden kann.</span><span class="sxs-lookup"><span data-stu-id="9339b-511">Both in-process and out-of-process hosting produce custom error pages when they fail to start the app.</span></span>

<span data-ttu-id="9339b-512">Wenn das ASP.NET Core-Modul weder den In-Process- noch den Out-of-Process-Anforderungshandler finden kann, wird die Statuscodeseite *500.0: Fehler beim Laden des In-Process-/Out-Of-Process-Handlers* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9339b-512">If the ASP.NET Core Module fails to find either the in-process or out-of-process request handler, a *500.0 - In-Process/Out-Of-Process Handler Load Failure* status code page appears.</span></span>

<span data-ttu-id="9339b-513">Wenn das ASP.NET Core-Modul beim In-Process-Hosting die App nicht starten kann, wird die Statuscodeseite *500.30: Fehler beim Starten* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9339b-513">For in-process hosting if the ASP.NET Core Module fails to start the app, a *500.30 - Start Failure* status code page appears.</span></span>

<span data-ttu-id="9339b-514">Wenn das ASP.NET Core-Modul beim Out-of-Process-Hosting den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9339b-514">For out-of-process hosting if the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span>

<span data-ttu-id="9339b-515">Um diese Seite zu unterdrücken und zur standardmäßigen IIS-5xx-Statuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="9339b-515">To suppress this page and revert to the default IIS 5xx status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="9339b-516">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="9339b-516">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="9339b-517">Protokollerstellung und Weiterleitung</span><span class="sxs-lookup"><span data-stu-id="9339b-517">Log creation and redirection</span></span>

<span data-ttu-id="9339b-518">Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-518">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="9339b-519">Ordner, die im `stdoutLogFile`-Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-519">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="9339b-520">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, in den die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\{APP POOL NAME}`, um die Schreibberechtigung bereitzustellen, wenn der Platzhalter `{APP POOL NAME}` der App-Poolname ist).</span><span class="sxs-lookup"><span data-stu-id="9339b-520">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="9339b-521">Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet.</span><span class="sxs-lookup"><span data-stu-id="9339b-521">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="9339b-522">Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.</span><span class="sxs-lookup"><span data-stu-id="9339b-522">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="9339b-523">Die Verwendung des stdout-Protokolls wird nur für die Problembehandlung bei App-Startproblemen beim Hosten in IIS oder beim Verwenden von [Support zum Zeitpunkt der Entwicklung für IIS in Visual Studio](xref:host-and-deploy/iis/development-time-iis-support) empfohlen, nicht aber für das lokale Debuggen und das Ausführen der App mit IIS Express.</span><span class="sxs-lookup"><span data-stu-id="9339b-523">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="9339b-524">Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken.</span><span class="sxs-lookup"><span data-stu-id="9339b-524">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="9339b-525">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="9339b-525">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="9339b-526">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="9339b-526">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="9339b-527">Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-527">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="9339b-528">Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung ( `.log`) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel `stdout`), getrennt durch Unterstriche, angehängt werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-528">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="9339b-529">Wenn der `stdoutLogFile`-Pfad mit `stdout` endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen `stdout_20180205194132_1934.log`.</span><span class="sxs-lookup"><span data-stu-id="9339b-529">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="9339b-530">Wenn `stdoutLogEnabled` „false“ ist, werden Fehler beim App-Start erfasst und in das Ereignisprotokoll mit bis zu 30 KB ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="9339b-530">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="9339b-531">Nach dem Start werden alle zusätzlichen Protokolle verworfen.</span><span class="sxs-lookup"><span data-stu-id="9339b-531">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="9339b-532">Das folgende Beispielelement `aspNetCore` konfiguriert die stdout-Protokollierung im relativen `.\log\`-Pfad.</span><span class="sxs-lookup"><span data-stu-id="9339b-532">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="9339b-533">Vergewissern Sie sich, dass die Identität des App-Poolbenutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.</span><span class="sxs-lookup"><span data-stu-id="9339b-533">Confirm that the app pool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="9339b-534">Beim Veröffentlichen einer App für die Azure App Service-Bereitstellung legt das Web SDK den Wert `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` fest.</span><span class="sxs-lookup"><span data-stu-id="9339b-534">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="9339b-535">Die Umgebungsvariable `%home` ist für Apps vordefiniert, die von Azure App Service gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-535">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="9339b-536">Weitere Informationen zu Pfadformaten finden Sie unter [Formate von Dateipfaden unter Windows-Systemen](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="9339b-536">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="9339b-537">Erweiterte Diagnoseprotokolle</span><span class="sxs-lookup"><span data-stu-id="9339b-537">Enhanced diagnostic logs</span></span>

<span data-ttu-id="9339b-538">Das ASP.NET Core-Modul kann so konfiguriert werden, dass es erweiterte Diagnoseprotokolle bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="9339b-538">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="9339b-539">Fügen Sie das Element `<handlerSettings>` zum Element `<aspNetCore>` in `web.config` hinzu.</span><span class="sxs-lookup"><span data-stu-id="9339b-539">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="9339b-540">Wenn `debugLevel` auf `TRACE` festgelegt wird, werden genauere Diagnoseinformationen zur Verfügung gestellt:</span><span class="sxs-lookup"><span data-stu-id="9339b-540">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="9339b-541">Ordner im Pfad, die für den `<handlerSetting>`-Wert (`logs` im vorherigen Beispiel) bereitgestellt werden, werden nicht automatisch vom Modul erstellt und müssen bereits in der Bereitstellung vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="9339b-541">Folders in the path provided to the `<handlerSetting>` value (`logs` in the preceding example) aren't created by the module automatically and should pre-exist in the deployment.</span></span> <span data-ttu-id="9339b-542">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, in den die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\{APP POOL NAME}`, um die Schreibberechtigung bereitzustellen, wenn der Platzhalter `{APP POOL NAME}` der App-Poolname ist).</span><span class="sxs-lookup"><span data-stu-id="9339b-542">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="9339b-543">`debugLevel`-Werte können sowohl die Ebene als auch den Speicherort enthalten.</span><span class="sxs-lookup"><span data-stu-id="9339b-543">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="9339b-544">Ebenen (von der geringsten zur höchsten Genauigkeit):</span><span class="sxs-lookup"><span data-stu-id="9339b-544">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="9339b-545">ERROR</span><span class="sxs-lookup"><span data-stu-id="9339b-545">ERROR</span></span>
* <span data-ttu-id="9339b-546">WARNING</span><span class="sxs-lookup"><span data-stu-id="9339b-546">WARNING</span></span>
* <span data-ttu-id="9339b-547">INFO</span><span class="sxs-lookup"><span data-stu-id="9339b-547">INFO</span></span>
* <span data-ttu-id="9339b-548">TRACE</span><span class="sxs-lookup"><span data-stu-id="9339b-548">TRACE</span></span>

<span data-ttu-id="9339b-549">Speicherorte (mehrere Speicherorte sind zulässig):</span><span class="sxs-lookup"><span data-stu-id="9339b-549">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="9339b-550">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="9339b-550">CONSOLE</span></span>
* <span data-ttu-id="9339b-551">EVENTLOG</span><span class="sxs-lookup"><span data-stu-id="9339b-551">EVENTLOG</span></span>
* <span data-ttu-id="9339b-552">DATEI</span><span class="sxs-lookup"><span data-stu-id="9339b-552">FILE</span></span>

<span data-ttu-id="9339b-553">Die Handlereinstellungen können auch über Umgebungsvariablen angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="9339b-553">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="9339b-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Dies ist der Pfad zur Debugprotokolldatei.</span><span class="sxs-lookup"><span data-stu-id="9339b-554">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="9339b-555">(Standard: `aspnetcore-debug.log`)</span><span class="sxs-lookup"><span data-stu-id="9339b-555">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="9339b-556">`ASPNETCORE_MODULE_DEBUG`: Dies ist die Einstellung der Debugebene.</span><span class="sxs-lookup"><span data-stu-id="9339b-556">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="9339b-557">Lassen Sie die Debugprotokollierung **nicht** länger als erforderlich für die Bereitstellung aktiviert, wenn Sie einen Fehler beheben.</span><span class="sxs-lookup"><span data-stu-id="9339b-557">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="9339b-558">Die Größe des Protokolls ist nicht beschränkt.</span><span class="sxs-lookup"><span data-stu-id="9339b-558">The size of the log isn't limited.</span></span> <span data-ttu-id="9339b-559">Wenn die Debugprotokollierung aktiviert bleibt, kann der verfügbare Speicherplatz aufgebraucht werden, und der Server- oder App-Dienst können abstürzen.</span><span class="sxs-lookup"><span data-stu-id="9339b-559">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="9339b-560">[Konfiguration mit der Datei „web.config“](#configuration-with-webconfig) enthält ein Beispiel für das `aspNetCore`-Element in der Datei `web.config`.</span><span class="sxs-lookup"><span data-stu-id="9339b-560">See [Configuration with web.config](#configuration-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="9339b-561">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="9339b-561">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="9339b-562">*Gilt nur für Out-of-Process-Hosting.*</span><span class="sxs-lookup"><span data-stu-id="9339b-562">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="9339b-563">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="9339b-563">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="9339b-564">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="9339b-564">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="9339b-565">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="9339b-565">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="9339b-566">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9339b-566">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="9339b-567">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9339b-567">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="9339b-568">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="9339b-568">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="9339b-569">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="9339b-569">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="9339b-570">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-570">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="9339b-571">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="9339b-571">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="9339b-572">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="9339b-572">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="9339b-573">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des `TrustedInstaller`-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9339b-573">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="9339b-574">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der `applicationHost.config`-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="9339b-574">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="9339b-575">Wenn eine ISS-Freigabekonfiguration auf demselben Computer verwendet wird wie die ISS-Installation, führen Sie das Installationsprogramm des ASP.NET -Core-Hosting-Pakets mit auf `1` festgelegtem Parameter `OPT_NO_SHARED_CONFIG_CHECK` aus:</span><span class="sxs-lookup"><span data-stu-id="9339b-575">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="9339b-576">Wenn sich der Pfad zur freigegebenen Konfiguration nicht auf demselben Computer wie die ISS-Installation befindet, befolgen Sie die folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="9339b-576">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="9339b-577">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="9339b-577">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="9339b-578">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="9339b-578">Run the installer.</span></span>
1. <span data-ttu-id="9339b-579">Exportieren Sie die aktualisierte Datei `applicationHost.config` auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="9339b-579">Export the updated `applicationHost.config` file to the share.</span></span>
1. <span data-ttu-id="9339b-580">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="9339b-580">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="9339b-581">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="9339b-581">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="9339b-582">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="9339b-582">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="9339b-583">Navigieren Sie auf dem Hostsystem zu `%windir%\System32\inetsrv`.</span><span class="sxs-lookup"><span data-stu-id="9339b-583">On the hosting system, navigate to `%windir%\System32\inetsrv`.</span></span>
1. <span data-ttu-id="9339b-584">Suchen Sie die Datei `aspnetcore.dll`.</span><span class="sxs-lookup"><span data-stu-id="9339b-584">Locate the `aspnetcore.dll` file.</span></span>
1. <span data-ttu-id="9339b-585">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="9339b-585">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="9339b-586">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="9339b-586">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="9339b-587">Die Installationsprotokolle des Hostingbundles für das Modul finden Sie unter `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span><span class="sxs-lookup"><span data-stu-id="9339b-587">The Hosting Bundle installer logs for the module are found at `C:\\Users\\%UserName%\\AppData\\Local\\Temp`.</span></span> <span data-ttu-id="9339b-588">Die Datei hat den Namen `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, wobei der Platzhalter `{TIMESTAMP}` der Zeitstempel ist.</span><span class="sxs-lookup"><span data-stu-id="9339b-588">The file is named `dd_DotNetCoreWinSvrHosting__\{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="9339b-589">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="9339b-589">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="9339b-590">Modul</span><span class="sxs-lookup"><span data-stu-id="9339b-590">Module</span></span>

<span data-ttu-id="9339b-591">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="9339b-591">**IIS (x86/amd64)**:</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="9339b-592">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="9339b-592">**IIS Express (x86/amd64)**:</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="9339b-593">Schema</span><span class="sxs-lookup"><span data-stu-id="9339b-593">Schema</span></span>

<span data-ttu-id="9339b-594">**IIS**</span><span class="sxs-lookup"><span data-stu-id="9339b-594">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="9339b-595">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="9339b-595">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="9339b-596">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="9339b-596">Configuration</span></span>

<span data-ttu-id="9339b-597">**IIS**</span><span class="sxs-lookup"><span data-stu-id="9339b-597">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="9339b-598">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="9339b-598">**IIS Express**</span></span>

* <span data-ttu-id="9339b-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="9339b-599">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="9339b-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="9339b-600">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="9339b-601">Den Speicherort dieser Dateien finden Sie, indem Sie `aspnetcore` in der Datei `applicationHost.config` suchen.</span><span class="sxs-lookup"><span data-stu-id="9339b-601">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9339b-602">Das ASP.NET Core-Modul ist ein natives IIS-Modul, das in die IIS-Pipeline integriert wird, um Webanforderungen an Back-End-ASP.NET Core-Apps weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="9339b-602">The ASP.NET Core Module is a native IIS module that plugs into the IIS pipeline to forward web requests to backend ASP.NET Core apps.</span></span>

<span data-ttu-id="9339b-603">Unterstützte Windows-Versionen:</span><span class="sxs-lookup"><span data-stu-id="9339b-603">Supported Windows versions:</span></span>

* <span data-ttu-id="9339b-604">Windows 7 oder höher</span><span class="sxs-lookup"><span data-stu-id="9339b-604">Windows 7 or later</span></span>
* <span data-ttu-id="9339b-605">Windows Server 2008 R2 oder höher</span><span class="sxs-lookup"><span data-stu-id="9339b-605">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="9339b-606">Das Modul funktioniert nur mit Kestrel.</span><span class="sxs-lookup"><span data-stu-id="9339b-606">The module only works with Kestrel.</span></span> <span data-ttu-id="9339b-607">Das Modul ist nicht mit [HTTP.sys](xref:fundamentals/servers/httpsys) kompatibel.</span><span class="sxs-lookup"><span data-stu-id="9339b-607">The module is incompatible with [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="9339b-608">Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul auch Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="9339b-608">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module also handles process management.</span></span> <span data-ttu-id="9339b-609">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie abstürzt.</span><span class="sxs-lookup"><span data-stu-id="9339b-609">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it crashes.</span></span> <span data-ttu-id="9339b-610">Dies ist im Prinzip das gleiche Verhalten wie bei ASP.NET 4.x-Apps, die prozessintern in IIS ausgeführt und durch [Windows Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-610">This is essentially the same behavior as seen with ASP.NET 4.x apps that run in-process in IIS that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="9339b-611">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer App:</span><span class="sxs-lookup"><span data-stu-id="9339b-611">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app:</span></span>

![ASP.NET Core-Modul](iis/index/_static/ancm-outofprocess.png)

<span data-ttu-id="9339b-613">Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-613">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="9339b-614">Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="9339b-614">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="9339b-615">Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.</span><span class="sxs-lookup"><span data-stu-id="9339b-615">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="9339b-616">Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguriert den Server so, dass er auf `http://localhost:{port}` lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-616">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="9339b-617">Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="9339b-617">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="9339b-618">Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="9339b-618">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="9339b-619">Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt.</span><span class="sxs-lookup"><span data-stu-id="9339b-619">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="9339b-620">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="9339b-620">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="9339b-621">Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen.</span><span class="sxs-lookup"><span data-stu-id="9339b-621">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="9339b-622">Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="9339b-622">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="9339b-623">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="9339b-623">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="9339b-624">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="9339b-624">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="9339b-625">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="9339b-625">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="9339b-626">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="9339b-626">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="9339b-627">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="9339b-627">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="9339b-628">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="9339b-628">Forward Windows authentication tokens.</span></span>

## <a name="how-to-install-and-use-the-aspnet-core-module"></a><span data-ttu-id="9339b-629">So installieren und verwenden Sie das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="9339b-629">How to install and use the ASP.NET Core Module</span></span>

<span data-ttu-id="9339b-630">Anweisungen zum Installieren des ASP.NET Core-Moduls finden Sie unter [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="9339b-630">For instructions on how to install the ASP.NET Core Module, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

## <a name="configuration-with-webconfig"></a><span data-ttu-id="9339b-631">Konfiguration mit der Datei „web.config“</span><span class="sxs-lookup"><span data-stu-id="9339b-631">Configuration with web.config</span></span>

<span data-ttu-id="9339b-632">Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei *web.config* der Site konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="9339b-632">The ASP.NET Core Module is configured with the `aspNetCore` section of the `system.webServer` node in the site's *web.config* file.</span></span>

<span data-ttu-id="9339b-633">Die folgende *web.config*-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:</span><span class="sxs-lookup"><span data-stu-id="9339b-633">The following *web.config* file is published for a [framework-dependent deployment](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) and configures the ASP.NET Core Module to handle site requests:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath="dotnet"
                arguments=".\MyApp.dll"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="9339b-634">Die folgende *web.config*-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="9339b-634">The following *web.config* is published for a [self-contained deployment](/dotnet/articles/core/deploying/#self-contained-deployments-scd):</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModule" resourceType="Unspecified" />
    </handlers>
    <aspNetCore processPath=".\MyApp.exe"
                stdoutLogEnabled="false"
                stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

<span data-ttu-id="9339b-635">Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt.</span><span class="sxs-lookup"><span data-stu-id="9339b-635">When an app is deployed to [Azure App Service](https://azure.microsoft.com/services/app-service/), the `stdoutLogFile` path is set to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="9339b-636">Der Pfad speichert stdout-Protokolle zum Ordner *LogFiles*, einem Speicherort, der automatisch vom Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-636">The path saves stdout logs to the *LogFiles* folder, which is a location automatically created by the service.</span></span>

<span data-ttu-id="9339b-637">Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.</span><span class="sxs-lookup"><span data-stu-id="9339b-637">For information on IIS sub-application configuration, see <xref:host-and-deploy/iis/index#sub-applications>.</span></span>

### <a name="attributes-of-the-aspnetcore-element"></a><span data-ttu-id="9339b-638">Attribute des aspNetCore-Elements</span><span class="sxs-lookup"><span data-stu-id="9339b-638">Attributes of the aspNetCore element</span></span>

| <span data-ttu-id="9339b-639">Attribut</span><span class="sxs-lookup"><span data-stu-id="9339b-639">Attribute</span></span> | <span data-ttu-id="9339b-640">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="9339b-640">Description</span></span> | <span data-ttu-id="9339b-641">Standard</span><span class="sxs-lookup"><span data-stu-id="9339b-641">Default</span></span> |
| --------- | ----------- | :-----: |
| `arguments` | <p><span data-ttu-id="9339b-642">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-642">Optional string attribute.</span></span></p><p><span data-ttu-id="9339b-643">Argumente zur ausführbaren Datei, die in **processPath** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="9339b-643">Arguments to the executable specified in **processPath**.</span></span></p>| |
| `disableStartUpErrorPage` | <p><span data-ttu-id="9339b-644">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-644">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9339b-645">Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei *web.config* konfigurierte Seite für den Statuscode 502 hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="9339b-645">If true, the **502.5 - Process Failure** page is suppressed, and the 502 status code page configured in the *web.config* takes precedence.</span></span></p> | `false` |
| `forwardWindowsAuthToken` | <p><span data-ttu-id="9339b-646">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-646">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9339b-647">Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an %ASPNETCORE_PORT% als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-647">If true, the token is forwarded to the child process listening on %ASPNETCORE_PORT% as a header 'MS-ASPNETCORE-WINAUTHTOKEN' per request.</span></span> <span data-ttu-id="9339b-648">Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="9339b-648">It's the responsibility of that process to call CloseHandle on this token per request.</span></span></p> | `true` |
| `processesPerApplication` | <p><span data-ttu-id="9339b-649">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-649">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-650">Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung **processPath** an, die aus der App gestartet werden können.</span><span class="sxs-lookup"><span data-stu-id="9339b-650">Specifies the number of instances of the process specified in the **processPath** setting that can be spun up per app.</span></span></p><p><span data-ttu-id="9339b-651">Einstellen von `processesPerApplication` wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="9339b-651">Setting `processesPerApplication` is discouraged.</span></span> <span data-ttu-id="9339b-652">Dieses Attribut wird in einer der nächsten Releases entfernt.</span><span class="sxs-lookup"><span data-stu-id="9339b-652">This attribute will be removed in a future release.</span></span></p> | <span data-ttu-id="9339b-653">Standardwert: `1`</span><span class="sxs-lookup"><span data-stu-id="9339b-653">Default: `1`</span></span><br><span data-ttu-id="9339b-654">Min.: `1`</span><span class="sxs-lookup"><span data-stu-id="9339b-654">Min: `1`</span></span><br><span data-ttu-id="9339b-655">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="9339b-655">Max: `100`</span></span> |
| `processPath` | <p><span data-ttu-id="9339b-656">Erforderliches Zeichenfolgenattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-656">Required string attribute.</span></span></p><p><span data-ttu-id="9339b-657">Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-657">Path to the executable that launches a process listening for HTTP requests.</span></span> <span data-ttu-id="9339b-658">Relative Pfade werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9339b-658">Relative paths are supported.</span></span> <span data-ttu-id="9339b-659">Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="9339b-659">If the path begins with `.`, the path is considered to be relative to the site root.</span></span></p> | |
| `rapidFailsPerMinute` | <p><span data-ttu-id="9339b-660">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-660">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-661">Gibt an, wie viele Abstürze des in **ProcessPath** angegebenen Prozesses pro Minute zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="9339b-661">Specifies the number of times the process specified in **processPath** is allowed to crash per minute.</span></span> <span data-ttu-id="9339b-662">Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</span><span class="sxs-lookup"><span data-stu-id="9339b-662">If this limit is exceeded, the module stops launching the process for the remainder of the minute.</span></span></p> | <span data-ttu-id="9339b-663">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="9339b-663">Default: `10`</span></span><br><span data-ttu-id="9339b-664">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="9339b-664">Min: `0`</span></span><br><span data-ttu-id="9339b-665">Max.: `100`</span><span class="sxs-lookup"><span data-stu-id="9339b-665">Max: `100`</span></span> |
| `requestTimeout` | <p><span data-ttu-id="9339b-666">Optionales TimeSpan-Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-666">Optional timespan attribute.</span></span></p><p><span data-ttu-id="9339b-667">Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-667">Specifies the duration for which the ASP.NET Core Module waits for a response from the process listening on %ASPNETCORE_PORT%.</span></span></p><p><span data-ttu-id="9339b-668">In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="9339b-668">In versions of the ASP.NET Core Module that shipped with the release of ASP.NET Core 2.1 or later, the `requestTimeout` is specified in hours, minutes, and seconds.</span></span></p> | <span data-ttu-id="9339b-669">Standardwert: `00:02:00`</span><span class="sxs-lookup"><span data-stu-id="9339b-669">Default: `00:02:00`</span></span><br><span data-ttu-id="9339b-670">Min.: `00:00:00`</span><span class="sxs-lookup"><span data-stu-id="9339b-670">Min: `00:00:00`</span></span><br><span data-ttu-id="9339b-671">Max.: `360:00:00`</span><span class="sxs-lookup"><span data-stu-id="9339b-671">Max: `360:00:00`</span></span> |
| `shutdownTimeLimit` | <p><span data-ttu-id="9339b-672">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-672">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-673">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei *app_offline.htm* erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-673">Duration in seconds that the module waits for the executable to gracefully shutdown when the *app_offline.htm* file is detected.</span></span></p> | <span data-ttu-id="9339b-674">Standardwert: `10`</span><span class="sxs-lookup"><span data-stu-id="9339b-674">Default: `10`</span></span><br><span data-ttu-id="9339b-675">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="9339b-675">Min: `0`</span></span><br><span data-ttu-id="9339b-676">Max.: `600`</span><span class="sxs-lookup"><span data-stu-id="9339b-676">Max: `600`</span></span> |
| `startupTimeLimit` | <p><span data-ttu-id="9339b-677">Optionales Ganzzahlattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-677">Optional integer attribute.</span></span></p><p><span data-ttu-id="9339b-678">Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht.</span><span class="sxs-lookup"><span data-stu-id="9339b-678">Duration in seconds that the module waits for the executable to start a process listening on the port.</span></span> <span data-ttu-id="9339b-679">Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</span><span class="sxs-lookup"><span data-stu-id="9339b-679">If this time limit is exceeded, the module kills the process.</span></span> <span data-ttu-id="9339b-680">Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann **RapidFailsPerMinute**-Anzahl in der letzten fortlaufenden Minute nicht starten.</span><span class="sxs-lookup"><span data-stu-id="9339b-680">The module attempts to relaunch the process when it receives a new request and continues to attempt to restart the process on subsequent incoming requests unless the app fails to start **rapidFailsPerMinute** number of times in the last rolling minute.</span></span></p><p><span data-ttu-id="9339b-681">Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</span><span class="sxs-lookup"><span data-stu-id="9339b-681">A value of 0 (zero) is **not** considered an infinite timeout.</span></span></p> | <span data-ttu-id="9339b-682">Standardwert: `120`</span><span class="sxs-lookup"><span data-stu-id="9339b-682">Default: `120`</span></span><br><span data-ttu-id="9339b-683">Min.: `0`</span><span class="sxs-lookup"><span data-stu-id="9339b-683">Min: `0`</span></span><br><span data-ttu-id="9339b-684">Max.: `3600`</span><span class="sxs-lookup"><span data-stu-id="9339b-684">Max: `3600`</span></span> |
| `stdoutLogEnabled` | <p><span data-ttu-id="9339b-685">Optionales boolesches Attribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-685">Optional Boolean attribute.</span></span></p><p><span data-ttu-id="9339b-686">Wenn TRUE, werden **stdout** und **stderr** für den Prozess, der in **processPath** angegeben wurde, zu der Datei weitergeleitet, die in **stdoutLogFile** angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="9339b-686">If true, **stdout** and **stderr** for the process specified in **processPath** are redirected to the file specified in **stdoutLogFile**.</span></span></p> | `false` |
| `stdoutLogFile` | <p><span data-ttu-id="9339b-687">Optionales Zeichenfolgeattribut.</span><span class="sxs-lookup"><span data-stu-id="9339b-687">Optional string attribute.</span></span></p><p><span data-ttu-id="9339b-688">Gibt den relativen oder absoluten Pfad an, für den **stdout** und **stderr** aus dem in **ProcessPath** angegebenen Prozess protokolliert wurden.</span><span class="sxs-lookup"><span data-stu-id="9339b-688">Specifies the relative or absolute file path for which **stdout** and **stderr** from the process specified in **processPath** are logged.</span></span> <span data-ttu-id="9339b-689">Relative Pfade sind relativ zum Stamm der Site.</span><span class="sxs-lookup"><span data-stu-id="9339b-689">Relative paths are relative to the root of the site.</span></span> <span data-ttu-id="9339b-690">Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt.</span><span class="sxs-lookup"><span data-stu-id="9339b-690">Any path starting with `.` are relative to the site root and all other paths are treated as absolute paths.</span></span> <span data-ttu-id="9339b-691">Alle im Pfad angegebenen Ordner müssen vorhanden sein, damit das Modul die Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="9339b-691">Any folders provided in the path must exist in order for the module to create the log file.</span></span> <span data-ttu-id="9339b-692">Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung ( *.log*) dem letzten Segment des Pfads **stdoutlogfile** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9339b-692">Using underscore delimiters, a timestamp, process ID, and file extension (*.log*) are added to the last segment of the **stdoutLogFile** path.</span></span> <span data-ttu-id="9339b-693">Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als *stdout_20180205194132_1934.log* im Ordner *logs* gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="9339b-693">If `.\logs\stdout` is supplied as a value, an example stdout log is saved as *stdout_20180205194132_1934.log* in the *logs* folder when saved on 2/5/2018 at 19:41:32 with a process ID of 1934.</span></span></p> | `aspnetcore-stdout` |

### <a name="setting-environment-variables"></a><span data-ttu-id="9339b-694">Festlegen von Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="9339b-694">Setting environment variables</span></span>

<span data-ttu-id="9339b-695">Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-695">Environment variables can be specified for the process in the `processPath` attribute.</span></span> <span data-ttu-id="9339b-696">Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an.</span><span class="sxs-lookup"><span data-stu-id="9339b-696">Specify an environment variable with the `<environmentVariable>` child element of an `<environmentVariables>` collection element.</span></span>

> [!WARNING]
> <span data-ttu-id="9339b-697">In diesem Abschnitt festgelegte Umgebungsvariablen stehen im Konflikt mit Systemumgebungsvariablen gleichen Namens.</span><span class="sxs-lookup"><span data-stu-id="9339b-697">Environment variables set in this section conflict with system environment variables set with the same name.</span></span> <span data-ttu-id="9339b-698">Wenn eine Umgebungsvariable sowohl in der Datei *web.config* als auch auf Systemebene in Windows festgelegt ist, wird der Wert aus der Datei *web.config* dem Wert der Systemumgebungsvariablen angefügt (z.B. `ASPNETCORE_ENVIRONMENT: Development;Development`), um zu verhindern, dass die App startet.</span><span class="sxs-lookup"><span data-stu-id="9339b-698">If an environment variable is set in both the *web.config* file and at the system level in Windows, the value from the *web.config* file becomes appended to the system environment variable value (for example, `ASPNETCORE_ENVIRONMENT: Development;Development`), which prevents the app from starting.</span></span>

<span data-ttu-id="9339b-699">Im folgenden Beispiel werden zwei Umgebungsvariablen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9339b-699">The following example sets two environment variables.</span></span> <span data-ttu-id="9339b-700">`ASPNETCORE_ENVIRONMENT` konfiguriert die Umgebung der App als `Development`.</span><span class="sxs-lookup"><span data-stu-id="9339b-700">`ASPNETCORE_ENVIRONMENT` configures the app's environment to `Development`.</span></span> <span data-ttu-id="9339b-701">Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei *web.config*, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="9339b-701">A developer may temporarily set this value in the *web.config* file in order to force the [Developer Exception Page](xref:fundamentals/error-handling) to load when debugging an app exception.</span></span> <span data-ttu-id="9339b-702">`CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.</span><span class="sxs-lookup"><span data-stu-id="9339b-702">`CONFIG_DIR` is an example of a user-defined environment variable, where the developer has written code that reads the value on startup to form a path for loading the app's configuration file.</span></span>

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile="\\?\%home%\LogFiles\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!WARNING]
> <span data-ttu-id="9339b-703">Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="9339b-703">Only set the `ASPNETCORE_ENVIRONMENT` environment variable to `Development` on staging and testing servers that aren't accessible to untrusted networks, such as the Internet.</span></span>

## <a name="app_offlinehtm"></a><span data-ttu-id="9339b-704">app_offline.htm</span><span class="sxs-lookup"><span data-stu-id="9339b-704">app_offline.htm</span></span>

<span data-ttu-id="9339b-705">Wenn eine Datei mit dem Namen *app_offline.htm* im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="9339b-705">If a file with the name *app_offline.htm* is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shutdown the app and stop processing incoming requests.</span></span> <span data-ttu-id="9339b-706">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurden, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="9339b-706">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module kills the running process.</span></span>

<span data-ttu-id="9339b-707">Wenn die Datei *app_offline.htm* vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der *app_offline.htm*-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="9339b-707">While the *app_offline.htm* file is present, the ASP.NET Core Module responds to requests by sending back the contents of the *app_offline.htm* file.</span></span> <span data-ttu-id="9339b-708">Wenn die Datei *app_offline.htm* entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="9339b-708">When the *app_offline.htm* file is removed, the next request starts the app.</span></span>

## <a name="start-up-error-page"></a><span data-ttu-id="9339b-709">Startfehler-Seite</span><span class="sxs-lookup"><span data-stu-id="9339b-709">Start-up error page</span></span>

<span data-ttu-id="9339b-710">Wenn das ASP.NET Core-Modul den Back-End-Prozess nicht starten kann oder der Back-End-Prozess gestartet wird, aber nicht am konfigurierten Port lauscht, wird die Statuscodeseite *502.5: Prozessfehler* angezeigt.</span><span class="sxs-lookup"><span data-stu-id="9339b-710">If the ASP.NET Core Module fails to launch the backend process or the backend process starts but fails to listen on the configured port, a *502.5 - Process Failure* status code page appears.</span></span> <span data-ttu-id="9339b-711">Um diese Seite zu unterdrücken und zur IIS-502-Sandardstatuscodeseite zurückzukehren, verwenden Sie das Attribut `disableStartUpErrorPage`.</span><span class="sxs-lookup"><span data-stu-id="9339b-711">To suppress this page and revert to the default IIS 502 status code page, use the `disableStartUpErrorPage` attribute.</span></span> <span data-ttu-id="9339b-712">Weitere Informationen zum Konfigurieren von benutzerdefinierten Fehlermeldungen finden Sie unter [HTTP-Fehler \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span><span class="sxs-lookup"><span data-stu-id="9339b-712">For more information on configuring custom error messages, see [HTTP Errors \<httpErrors>](/iis/configuration/system.webServer/httpErrors/).</span></span>

## <a name="log-creation-and-redirection"></a><span data-ttu-id="9339b-713">Protokollerstellung und Weiterleitung</span><span class="sxs-lookup"><span data-stu-id="9339b-713">Log creation and redirection</span></span>

<span data-ttu-id="9339b-714">Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-714">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="9339b-715">Ordner, die im `stdoutLogFile`-Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-715">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="9339b-716">Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, an dem die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\<app_pool_name>`, um die Schreibberechtigung bereitzustellen).</span><span class="sxs-lookup"><span data-stu-id="9339b-716">The app pool must have write access to the location where the logs are written (use `IIS AppPool\<app_pool_name>` to provide write permission).</span></span>

<span data-ttu-id="9339b-717">Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet.</span><span class="sxs-lookup"><span data-stu-id="9339b-717">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="9339b-718">Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.</span><span class="sxs-lookup"><span data-stu-id="9339b-718">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="9339b-719">Die Verwendung des stdout-Protokolls wird nur für die Problembehandlung bei App-Startproblemen beim Hosten in IIS oder beim Verwenden von [Support zum Zeitpunkt der Entwicklung für IIS in Visual Studio](xref:host-and-deploy/iis/development-time-iis-support) empfohlen, nicht aber für das lokale Debuggen und das Ausführen der App mit IIS Express.</span><span class="sxs-lookup"><span data-stu-id="9339b-719">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="9339b-720">Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken.</span><span class="sxs-lookup"><span data-stu-id="9339b-720">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="9339b-721">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="9339b-721">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="9339b-722">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="9339b-722">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="9339b-723">Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9339b-723">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="9339b-724">Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung ( *.log*) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel *stdout*), getrennt durch Unterstriche, angehängt werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-724">The log file name is composed by appending the timestamp, process ID, and file extension (*.log*) to the last segment of the `stdoutLogFile` path (typically *stdout*) delimited by underscores.</span></span> <span data-ttu-id="9339b-725">Wenn der `stdoutLogFile`-Pfad mit *stdout* endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen *stdout_20180205194132_1934.log*.</span><span class="sxs-lookup"><span data-stu-id="9339b-725">If the `stdoutLogFile` path ends with *stdout*, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name *stdout_20180205194132_1934.log*.</span></span>

<span data-ttu-id="9339b-726">Das folgende Beispielelement `aspNetCore` konfiguriert die stdout-Protokollierung im relativen `.\log\`-Pfad.</span><span class="sxs-lookup"><span data-stu-id="9339b-726">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="9339b-727">Vergewissern Sie sich, dass die Identität des AppPool-Benutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.</span><span class="sxs-lookup"><span data-stu-id="9339b-727">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout">
</aspNetCore>
```

<span data-ttu-id="9339b-728">Beim Veröffentlichen einer App für die Azure App Service-Bereitstellung legt das Web SDK den Wert `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` fest.</span><span class="sxs-lookup"><span data-stu-id="9339b-728">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="9339b-729">Die Umgebungsvariable `%home` ist für Apps vordefiniert, die von Azure App Service gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-729">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="9339b-730">Informationen zum Erstellen von Protokollierungsfilterregeln finden Sie in den Abschnitten [Konfiguration](xref:fundamentals/logging/index#log-filtering) und [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) der Dokumentation zur ASP.NET Core-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="9339b-730">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="9339b-731">Weitere Informationen zu Pfadformaten finden Sie unter [Formate von Dateipfaden unter Windows-Systemen](/dotnet/standard/io/file-path-formats).</span><span class="sxs-lookup"><span data-stu-id="9339b-731">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="9339b-732">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="9339b-732">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="9339b-733">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="9339b-733">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="9339b-734">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="9339b-734">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="9339b-735">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="9339b-735">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="9339b-736">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9339b-736">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="9339b-737">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9339b-737">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="9339b-738">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="9339b-738">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="9339b-739">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="9339b-739">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="9339b-740">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="9339b-740">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="9339b-741">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="9339b-741">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="9339b-742">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="9339b-742">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="9339b-743">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des **TrustedInstaller**-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="9339b-743">The ASP.NET Core Module installer runs with the privileges of the **TrustedInstaller** account.</span></span> <span data-ttu-id="9339b-744">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der *applicationHost.config*-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="9339b-744">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the *applicationHost.config* file on the share.</span></span>

<span data-ttu-id="9339b-745">Wenn Sie eine IIS-Freigabekonfiguration verwenden, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="9339b-745">When using an IIS Shared Configuration, follow these steps:</span></span>

1. <span data-ttu-id="9339b-746">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="9339b-746">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="9339b-747">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="9339b-747">Run the installer.</span></span>
1. <span data-ttu-id="9339b-748">Exportieren Sie die aktualisierte Datei *applicationHost.config* auf die Freigabe.</span><span class="sxs-lookup"><span data-stu-id="9339b-748">Export the updated *applicationHost.config* file to the share.</span></span>
1. <span data-ttu-id="9339b-749">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="9339b-749">Re-enable the IIS Shared Configuration.</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="9339b-750">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="9339b-750">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="9339b-751">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="9339b-751">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="9339b-752">Navigieren Sie auf dem Hostsystem zu *%windir%\System32\inetsrv*.</span><span class="sxs-lookup"><span data-stu-id="9339b-752">On the hosting system, navigate to *%windir%\System32\inetsrv*.</span></span>
1. <span data-ttu-id="9339b-753">Suchen Sie die Datei *aspnetcore.dll*.</span><span class="sxs-lookup"><span data-stu-id="9339b-753">Locate the *aspnetcore.dll* file.</span></span>
1. <span data-ttu-id="9339b-754">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="9339b-754">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="9339b-755">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="9339b-755">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="9339b-756">Die Installer-Protokolle des Hostingpakets für das Modul finden Sie unter *C:\\Benutzer\\%UserName%\\AppData\\Local\\Temp*. Die Datei heißt *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span><span class="sxs-lookup"><span data-stu-id="9339b-756">The Hosting Bundle installer logs for the module are found at *C:\\Users\\%UserName%\\AppData\\Local\\Temp*. The file is named *dd_DotNetCoreWinSvrHosting__\<timestamp>_000_AspNetCoreModule_x64.log*.</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="9339b-757">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="9339b-757">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="9339b-758">Modul</span><span class="sxs-lookup"><span data-stu-id="9339b-758">Module</span></span>

<span data-ttu-id="9339b-759">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="9339b-759">**IIS (x86/amd64):**</span></span>

* <span data-ttu-id="9339b-760">%windir%\System32\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="9339b-760">%windir%\System32\inetsrv\aspnetcore.dll</span></span>

* <span data-ttu-id="9339b-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="9339b-761">%windir%\SysWOW64\inetsrv\aspnetcore.dll</span></span>

<span data-ttu-id="9339b-762">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="9339b-762">**IIS Express (x86/amd64):**</span></span>

* <span data-ttu-id="9339b-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="9339b-763">%ProgramFiles%\IIS Express\aspnetcore.dll</span></span>

* <span data-ttu-id="9339b-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span><span class="sxs-lookup"><span data-stu-id="9339b-764">%ProgramFiles(x86)%\IIS Express\aspnetcore.dll</span></span>

### <a name="schema"></a><span data-ttu-id="9339b-765">Schema</span><span class="sxs-lookup"><span data-stu-id="9339b-765">Schema</span></span>

<span data-ttu-id="9339b-766">**IIS**</span><span class="sxs-lookup"><span data-stu-id="9339b-766">**IIS**</span></span>

* <span data-ttu-id="9339b-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="9339b-767">%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml</span></span>

<span data-ttu-id="9339b-768">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="9339b-768">**IIS Express**</span></span>

* <span data-ttu-id="9339b-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span><span class="sxs-lookup"><span data-stu-id="9339b-769">%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml</span></span>

### <a name="configuration"></a><span data-ttu-id="9339b-770">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="9339b-770">Configuration</span></span>

<span data-ttu-id="9339b-771">**IIS**</span><span class="sxs-lookup"><span data-stu-id="9339b-771">**IIS**</span></span>

* <span data-ttu-id="9339b-772">%windir%\System32\inetsrv\config\applicationHost.config</span><span class="sxs-lookup"><span data-stu-id="9339b-772">%windir%\System32\inetsrv\config\applicationHost.config</span></span>

<span data-ttu-id="9339b-773">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="9339b-773">**IIS Express**</span></span>

* <span data-ttu-id="9339b-774">Visual Studio: {ANWENDUNGSSTAMM}\\.vs\config\applicationHost.config.</span><span class="sxs-lookup"><span data-stu-id="9339b-774">Visual Studio: {APPLICATION ROOT}\\.vs\config\applicationHost.config</span></span>

* <span data-ttu-id="9339b-775">*iisexpress.exe*-CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span><span class="sxs-lookup"><span data-stu-id="9339b-775">*iisexpress.exe* CLI: %USERPROFILE%\Documents\IISExpress\config\applicationhost.config</span></span>

<span data-ttu-id="9339b-776">Den Speicherort dieser Dateien finden Sie, indem Sie *aspnetcore* in der Datei *applicationHost.config* suchen.</span><span class="sxs-lookup"><span data-stu-id="9339b-776">The files can be found by searching for *aspnetcore* in the *applicationHost.config* file.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9339b-777">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9339b-777">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <span data-ttu-id="9339b-778">[Referenzquelle für das ASP.NET Core-Modul (Standardbranch „master“)](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Verwenden Sie die Dropdownliste **Branch**, um ein spezifisches Release auszuwählen (z. B. `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="9339b-778">[ASP.NET Core Module reference source [default branch (master)]](https://github.com/dotnet/aspnetcore/tree/master/src/Servers/IIS/AspNetCoreModuleV2): Use the **Branch** drop down list to select a specific release (for example, `release/3.1`).</span></span>
* <xref:host-and-deploy/iis/modules>
