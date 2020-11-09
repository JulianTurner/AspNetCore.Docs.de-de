---
title: In-Process-Hosting mit IIS und ASP.NET Core
author: rick-anderson
description: Hier erfahren Sie mehr über das In-Process-Hosting mit IIS und dem ASP.NET Core-Modul.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/in-process-hosting
ms.openlocfilehash: 47dc6f65f398ecce45c563c359dfde6e17d1dc1b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058479"
---
# <a name="in-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="a8e7a-103">In-Process-Hosting mit IIS und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8e7a-103">In-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="a8e7a-104">Beim Einsatz von In-Process-Hosting wird eine ASP.NET Core-App im gleichen Prozess wie ihr IIS-Workerprozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-104">In-process hosting runs an ASP.NET Core app in the same process as its IIS worker process.</span></span> <span data-ttu-id="a8e7a-105">Durch das In-Process-Hosting wird die Leistung des Out-of-Process-Hosting verbessert, da Anforderungen nicht per Proxy über den Loopbackadapter weitergeleitet werden. Dabei handelt es sich um eine Netzwerkschnittstelle, die ausgehenden Netzwerkdatenverkehr zum selben Computer zurück leitet.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-105">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span>

<span data-ttu-id="a8e7a-106">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer In-Process gehosteten App:</span><span class="sxs-lookup"><span data-stu-id="a8e7a-106">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted in-process:</span></span>

![Das ASP.NET Core-Modul im In-Process-Hostingszenario](index/_static/ancm-inprocess.png)

## <a name="enable-in-process-hosting"></a><span data-ttu-id="a8e7a-108">Aktivieren von In-Process-Hosting</span><span class="sxs-lookup"><span data-stu-id="a8e7a-108">Enable in-process hosting</span></span>

<span data-ttu-id="a8e7a-109">Seit ASP.NET Core 3.0 ist In-Process-Hosting standardmäßig für alle für die IIS bereitgestellten Apps aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-109">Since ASP.NET Core 3.0, in-process hosting has been enabled by default for all app deployed to IIS.</span></span>

<span data-ttu-id="a8e7a-110">Legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft in der Projektdatei (`.csproj`) auf `InProcess` fest, um eine App explizit für In-Process-Hosting zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="a8e7a-110">To explicitly configure an app for in-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `InProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>InProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

## <a name="general-architecture"></a><span data-ttu-id="a8e7a-111">Allgemeine Architektur</span><span class="sxs-lookup"><span data-stu-id="a8e7a-111">General architecture</span></span>

<span data-ttu-id="a8e7a-112">Der allgemeine Anforderungsflow lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="a8e7a-112">The general flow of a request is as follows:</span></span>

1. <span data-ttu-id="a8e7a-113">Eine Anforderung geht aus dem Web beim HTTP.sys-Treiber im Kernelmodus ein.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-113">A request arrives from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="a8e7a-114">Der Treiber leitet die native Anforderung an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="a8e7a-114">The driver routes the native request to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="a8e7a-115">Das ASP.NET Core-Modul empfängt die native Anforderung und übergibt sie an den IIS-HTTP-Server (`IISHttpServer`).</span><span class="sxs-lookup"><span data-stu-id="a8e7a-115">The ASP.NET Core Module receives the native request and passes it to IIS HTTP Server (`IISHttpServer`).</span></span> <span data-ttu-id="a8e7a-116">Der IIS-HTTP-Server ist eine prozessinterne Serverimplementierung für IIS, die die Anforderung vom nativen Modus in den verwalteten Modus konvertiert.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-116">IIS HTTP Server is an in-process server implementation for IIS that converts the request from native to managed.</span></span>

<span data-ttu-id="a8e7a-117">Nach der Verarbeitung der Anforderung durch den IIS-HTTP-Server:</span><span class="sxs-lookup"><span data-stu-id="a8e7a-117">After the IIS HTTP Server processes the request:</span></span>

1. <span data-ttu-id="a8e7a-118">Die Anforderung wird an die ASP.NET Core-Middlewarepipeline gesendet.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-118">The request is sent to the ASP.NET Core middleware pipeline.</span></span>
1. <span data-ttu-id="a8e7a-119">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-119">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span>
1. <span data-ttu-id="a8e7a-120">Die Antwort der App wird über den IIS-HTTP-Server zurück an IIS übergeben.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-120">The app's response is passed back to IIS through IIS HTTP Server.</span></span>
1. <span data-ttu-id="a8e7a-121">IIS übermittelt die Antwort an den Client, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-121">IIS sends the response to the client that initiated the request.</span></span>

<span data-ttu-id="a8e7a-122">`CreateDefaultBuilder` fügt eine <xref:Microsoft.AspNetCore.Hosting.Server.IServer>-Instanz hinzu, indem die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A>-Methode aufgerufen wird, um die [CoreCLR](/dotnet/standard/glossary#coreclr) zu starten und die App im IIS-Workerprozess zu hosten (`w3wp.exe` oder `iisexpress.exe`).</span><span class="sxs-lookup"><span data-stu-id="a8e7a-122">`CreateDefaultBuilder` adds an <xref:Microsoft.AspNetCore.Hosting.Server.IServer> instance by calling the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> method to boot the [CoreCLR](/dotnet/standard/glossary#coreclr) and host the app inside of the IIS worker process (`w3wp.exe` or `iisexpress.exe`).</span></span> <span data-ttu-id="a8e7a-123">Leistungstests weisen darauf hin, dass das In-Process-Hosting einer .NET Core-App einen weitaus höheren Anforderungsdurchsatz im Vergleich zum Out-of-Process-Hosting der App mit Weiterleitung der Anforderungen über einen Proxy an [Kestrel](xref:fundamentals/servers/kestrel) bietet.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-123">Performance tests indicate that hosting a .NET Core app in-process delivers significantly higher request throughput compared to hosting the app out-of-process and proxying requests to [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="a8e7a-124">Apps, die als einzelne ausführbare Datei veröffentlicht wurden, können vom In-Prozess-Hostingmodell nicht geladen werden.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-124">Apps published as a single file executable can't be loaded by the in-process hosting model.</span></span>

## <a name="application-configuration"></a><span data-ttu-id="a8e7a-125">Anwendungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="a8e7a-125">Application configuration</span></span>

<span data-ttu-id="a8e7a-126">Schließen Sie zur Konfiguration von IIS-Optionen eine Dienstkonfiguration für <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> ein.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-126">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="a8e7a-127">Mit dem folgenden Beispiel wird AutomaticAuthentication deaktiviert:</span><span class="sxs-lookup"><span data-stu-id="a8e7a-127">The following example disables AutomaticAuthentication:</span></span>

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| <span data-ttu-id="a8e7a-128">Option</span><span class="sxs-lookup"><span data-stu-id="a8e7a-128">Option</span></span> | <span data-ttu-id="a8e7a-129">Standard</span><span class="sxs-lookup"><span data-stu-id="a8e7a-129">Default</span></span> | <span data-ttu-id="a8e7a-130">Einstellung</span><span class="sxs-lookup"><span data-stu-id="a8e7a-130">Setting</span></span> |
| ------ | :-----: | ------- |
| `AutomaticAuthentication` | `true` | <span data-ttu-id="a8e7a-131">Bei Festlegung auf `true` legt der Server den per [Windows-Authentifizierung](xref:security/authentication/windowsauth) authentifizierten `HttpContext.User` fest.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-131">If `true`, IIS Server sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="a8e7a-132">Bei Festlegung auf `false` stellt der Server nur eine Identität für `HttpContext.User` bereit und antwortet auf explizite Anforderungen durch `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-132">If `false`, the server only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="a8e7a-133">Die Windows-Authentifizierung muss in IIS aktiviert sein, damit `AutomaticAuthentication` funktioniert.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-133">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="a8e7a-134">Weitere Informationen finden Sie unter [Windows-Authentifizierung](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="a8e7a-134">For more information, see [Windows Authentication](xref:security/authentication/windowsauth).</span></span> |
| `AuthenticationDisplayName` | `null` | <span data-ttu-id="a8e7a-135">Legt den Anzeigename fest, der Benutzern auf Anmeldungsseiten angezeigt wird</span><span class="sxs-lookup"><span data-stu-id="a8e7a-135">Sets the display name shown to users on login pages.</span></span> |
| `AllowSynchronousIO` | `false` | <span data-ttu-id="a8e7a-136">Steuert, ob synchrone E/A-Vorgänge für `HttpContext.Request` und `HttpContext.Response` zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-136">Whether synchronous I/O is allowed for the `HttpContext.Request` and the `HttpContext.Response`.</span></span> |
| `MaxRequestBodySize` | `30000000` | <span data-ttu-id="a8e7a-137">Dient zum Abrufen oder Festlegen der maximalen Größe des Anforderungstexts für `HttpRequest`.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-137">Gets or sets the max request body size for the `HttpRequest`.</span></span> <span data-ttu-id="a8e7a-138">Beachten Sie, dass IIS selbst das Limit `maxAllowedContentLength` aufweist, das vor der in den `IISServerOptions` festgelegten `MaxRequestBodySize` verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-138">Note that IIS itself has the limit `maxAllowedContentLength` which will be processed before the `MaxRequestBodySize` set in the `IISServerOptions`.</span></span> <span data-ttu-id="a8e7a-139">Das Ändern von `MaxRequestBodySize` hat keine Auswirkungen auf `maxAllowedContentLength`.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-139">Changing the `MaxRequestBodySize` won't affect the `maxAllowedContentLength`.</span></span> <span data-ttu-id="a8e7a-140">Zum Erhöhen von `maxAllowedContentLength` fügen Sie einen Eintrag in `web.config` hinzu, um `maxAllowedContentLength` auf einen höheren Wert festzulegen.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-140">To increase `maxAllowedContentLength`, add an entry in the `web.config` to set `maxAllowedContentLength` to a higher value.</span></span> <span data-ttu-id="a8e7a-141">Weitere Informationen hierzu finden Sie unter [Konfiguration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span><span class="sxs-lookup"><span data-stu-id="a8e7a-141">For more details, see [Configuration](/iis/configuration/system.webServer/security/requestFiltering/requestLimits/#configuration).</span></span> |

## <a name="differences-between-in-process-and-out-of-process-hosting"></a><span data-ttu-id="a8e7a-142">Unterschiede zwischen In-Process- und Out-of-Process-Hosting</span><span class="sxs-lookup"><span data-stu-id="a8e7a-142">Differences between in-process and out-of-process hosting</span></span>

<span data-ttu-id="a8e7a-143">Die folgenden Merkmale treffen für In-Process-Hosting zu:</span><span class="sxs-lookup"><span data-stu-id="a8e7a-143">The following characteristics apply when hosting in-process:</span></span>

* <span data-ttu-id="a8e7a-144">Der IIS-HTTP-Server (`IISHttpServer`) wird anstelle des [Kestrel](xref:fundamentals/servers/kestrel)-Servers verwendet.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-144">IIS HTTP Server (`IISHttpServer`) is used instead of [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="a8e7a-145">Beim In-Process-Hosting ruft [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> zu folgendem Zweck auf:</span><span class="sxs-lookup"><span data-stu-id="a8e7a-145">For in-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIIS%2A> to:</span></span>

  * <span data-ttu-id="a8e7a-146">Registrieren des `IISHttpServer`.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-146">Register the `IISHttpServer`.</span></span>
  * <span data-ttu-id="a8e7a-147">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-147">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
  * <span data-ttu-id="a8e7a-148">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-148">Configure the host to capture startup errors.</span></span>

* <span data-ttu-id="a8e7a-149">Das [`requestTimeout`-Attribut](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) trifft auf das In-Process-Hosting nicht zu.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-149">The [`requestTimeout` attribute](xref:host-and-deploy/iis/web-config#attributes-of-the-aspnetcore-element) doesn't apply to in-process hosting.</span></span>

* <span data-ttu-id="a8e7a-150">Das gemeinsame Verwenden eines Anwendungspools durch mehrere Apps wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-150">Sharing an app pool among apps isn't supported.</span></span> <span data-ttu-id="a8e7a-151">Verwenden Sie einen Anwendungspool pro App.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-151">Use one app pool per app.</span></span>

* <span data-ttu-id="a8e7a-152">Die Architektur (Bitbreite) der App und der installierten Runtime (x64 oder x86) müssen mit der Architektur des Anwendungspools übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-152">The architecture (bitness) of the app and installed runtime (x64 or x86) must match the architecture of the app pool.</span></span> <span data-ttu-id="a8e7a-153">Für Apps, die für 32 Bit (x86) veröffentlicht wurden, müssen z. B. 32 Bit für ihre IIS-Anwendungspools aktiviert sein.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-153">For example, apps published for 32-bit (x86) must have 32-bit enabled for their IIS Application Pools.</span></span> <span data-ttu-id="a8e7a-154">Weitere Informationen finden Sie im Abschnitt [Erstellen der IIS-Website](xref:tutorials/publish-to-iis#create-the-iis-site).</span><span class="sxs-lookup"><span data-stu-id="a8e7a-154">For more information, see the [Create the IIS site](xref:tutorials/publish-to-iis#create-the-iis-site) section.</span></span>

* <span data-ttu-id="a8e7a-155">Verbindungstrennungen von Clients werden erkannt.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-155">Client disconnects are detected.</span></span> <span data-ttu-id="a8e7a-156">Das [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A)-Abbruchtoken wird abgebrochen, wenn der Client die Verbindung trennt.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-156">The [`HttpContext.RequestAborted`](xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted%2A) cancellation token is cancelled when the client disconnects.</span></span>

* <span data-ttu-id="a8e7a-157">Beim In-Process-Hosting wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-157">When hosting in-process, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync%2A> isn't called internally to initialize a user.</span></span> <span data-ttu-id="a8e7a-158">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-158">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="a8e7a-159">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> auf, um Authentifizierungsdienste hinzuzufügen, wenn Ansprüche mit einer <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung transformiert werden:</span><span class="sxs-lookup"><span data-stu-id="a8e7a-159">When transforming claims with an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation, call <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> to add authentication services:</span></span>

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
  
* <span data-ttu-id="a8e7a-160">[Bereitstellungen von Webpaketen (Einzeldateien)](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a8e7a-160">[Web Package (single-file) deployments](/aspnet/web-forms/overview/deployment/web-deployment-in-the-enterprise/deploying-web-packages) aren't supported.</span></span>
