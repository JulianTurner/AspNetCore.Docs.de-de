---
title: Out-of-Process-Hosting mit IIS und ASP.NET Core
author: rick-anderson
description: Hier erfahren Sie mehr über das Out-of-Process-Hosting mit IIS und das ASP.NET Core-Modul.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/out-of-process-hosting
ms.openlocfilehash: 78ead27bd1373237d1c0a48655d73a41a0a72279
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060416"
---
# <a name="out-of-process-hosting-with-iis-and-aspnet-core"></a><span data-ttu-id="88d6b-103">Out-of-Process-Hosting mit IIS und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="88d6b-103">Out-of-process hosting with IIS and ASP.NET Core</span></span> 

<span data-ttu-id="88d6b-104">Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das ASP.NET Core-Modul die Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="88d6b-104">Because ASP.NET Core apps run in a process separate from the IIS worker process, the ASP.NET Core Module handles process management.</span></span> <span data-ttu-id="88d6b-105">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie heruntergefahren wird oder abstürzt.</span><span class="sxs-lookup"><span data-stu-id="88d6b-105">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="88d6b-106">Dies ist im Prinzip das gleiche Verhalten wie bei Apps, die prozessintern ausgeführt und durch den [Windows-Prozessaktivierungsdienst (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="88d6b-106">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="88d6b-107">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer Out-of-Process gehosteten App:</span><span class="sxs-lookup"><span data-stu-id="88d6b-107">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![Das ASP.NET Core-Modul im Out-of-Process-Hostingszenario](index/_static/ancm-outofprocess.png)

1. <span data-ttu-id="88d6b-109">Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.</span><span class="sxs-lookup"><span data-stu-id="88d6b-109">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span>
1. <span data-ttu-id="88d6b-110">Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter.</span><span class="sxs-lookup"><span data-stu-id="88d6b-110">The driver routes the requests to IIS on the website's configured port.</span></span> <span data-ttu-id="88d6b-111">Der konfigurierte Port ist normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="88d6b-111">The configured port is usually 80 (HTTP) or 443 (HTTPS).</span></span>
1. <span data-ttu-id="88d6b-112">Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter.</span><span class="sxs-lookup"><span data-stu-id="88d6b-112">The module forwards the requests to Kestrel on a random port for the app.</span></span> <span data-ttu-id="88d6b-113">Der zufällige Port entspricht nicht Port 80 oder 443.</span><span class="sxs-lookup"><span data-stu-id="88d6b-113">The random port isn't 80 or 443.</span></span>

<!-- make this a bullet list -->
<span data-ttu-id="88d6b-114">Das ASP.NET Core-Modul gibt den Port beim Start über eine Umgebungsvariable an.</span><span class="sxs-lookup"><span data-stu-id="88d6b-114">The ASP.NET Core Module specifies the port via an environment variable at startup.</span></span> <span data-ttu-id="88d6b-115">Mit der <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A>-Erweiterung wird der Server so konfiguriert, dass er an `http://localhost:{PORT}` lauscht.</span><span class="sxs-lookup"><span data-stu-id="88d6b-115">The <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> extension configures the server to listen on `http://localhost:{PORT}`.</span></span> <span data-ttu-id="88d6b-116">Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="88d6b-116">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="88d6b-117">Das Modul unterstützt die HTTPS-Weiterleitung nicht.</span><span class="sxs-lookup"><span data-stu-id="88d6b-117">The module doesn't support HTTPS forwarding.</span></span> <span data-ttu-id="88d6b-118">Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="88d6b-118">Requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="88d6b-119">Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung an die ASP.NET Core-Middlewarepipeline weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="88d6b-119">After Kestrel picks up the request from the module, the request is forwarded into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="88d6b-120">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="88d6b-120">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="88d6b-121">Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen.</span><span class="sxs-lookup"><span data-stu-id="88d6b-121">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="88d6b-122">Die Antwort der App wird dann an IIS zurückgegeben und an den HTTP-Client weitergeleitet, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="88d6b-122">The app's response is passed back to IIS, which forwards it back to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="88d6b-123">Einen Konfigurationsleitfaden für das ASP.NET Core-Modul finden Sie unter <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="88d6b-123">For ASP.NET Core Module configuration guidance, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="88d6b-124">Weitere Informationen zum Hosten finden Sie unter [Hosten in ASP.NET Core](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="88d6b-124">For more information on hosting, see [Host in ASP.NET Core](xref:fundamentals/index#host).</span></span>

## <a name="application-configuration"></a><span data-ttu-id="88d6b-125">Anwendungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="88d6b-125">Application configuration</span></span>

### <a name="enable-the-iisintegration-components"></a><span data-ttu-id="88d6b-126">Aktivieren der IISIntegration-Komponenten</span><span class="sxs-lookup"><span data-stu-id="88d6b-126">Enable the IISIntegration components</span></span>

<span data-ttu-id="88d6b-127">Rufen Sie beim Erstellen eines Hosts in `CreateHostBuilder` (`Program.cs`) <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> ab, um die IIS-Integration zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="88d6b-127">When building a host in `CreateHostBuilder` (`Program.cs`), call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to enable IIS integration:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        ...
```

<span data-ttu-id="88d6b-128">Weitere Informationen zu `CreateDefaultBuilder` finden Sie unter <xref:fundamentals/host/generic-host#default-builder-settings>.</span><span class="sxs-lookup"><span data-stu-id="88d6b-128">For more information on `CreateDefaultBuilder`, see <xref:fundamentals/host/generic-host#default-builder-settings>.</span></span>


<span data-ttu-id="88d6b-129">**Out-of-Process-Hostingmodell**</span><span class="sxs-lookup"><span data-stu-id="88d6b-129">**Out-of-process hosting model**</span></span>

<span data-ttu-id="88d6b-130">Schließen Sie zur Konfiguration von IIS-Optionen eine Dienstkonfiguration für <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A> ein.</span><span class="sxs-lookup"><span data-stu-id="88d6b-130">To configure IIS options, include a service configuration for <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices%2A>.</span></span> <span data-ttu-id="88d6b-131">Im folgenden Beispiel wird Verhindert, dass die App `HttpContext.Connection.ClientCertificate` auffüllt:</span><span class="sxs-lookup"><span data-stu-id="88d6b-131">The following example prevents the app from populating `HttpContext.Connection.ClientCertificate`:</span></span>

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| <span data-ttu-id="88d6b-132">Option</span><span class="sxs-lookup"><span data-stu-id="88d6b-132">Option</span></span>                         | <span data-ttu-id="88d6b-133">Standard</span><span class="sxs-lookup"><span data-stu-id="88d6b-133">Default</span></span> | <span data-ttu-id="88d6b-134">Einstellung</span><span class="sxs-lookup"><span data-stu-id="88d6b-134">Setting</span></span> |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | <span data-ttu-id="88d6b-135">Bei Festlegung auf `true` legt die [Middleware für die IIS-Integration](#enable-the-iisintegration-components) den per [Windows-Authentifizierung](xref:security/authentication/windowsauth) authentifizierten `HttpContext.User` fest.</span><span class="sxs-lookup"><span data-stu-id="88d6b-135">If `true`, [IIS Integration Middleware](#enable-the-iisintegration-components) sets the `HttpContext.User` authenticated by [Windows Authentication](xref:security/authentication/windowsauth).</span></span> <span data-ttu-id="88d6b-136">Bei Festlegung auf `false` stellt die Middleware nur eine Identität für `HttpContext.User` bereit und antwortet auf explizite Anforderungen durch `AuthenticationScheme`.</span><span class="sxs-lookup"><span data-stu-id="88d6b-136">If `false`, the middleware only provides an identity for `HttpContext.User` and responds to challenges when explicitly requested by the `AuthenticationScheme`.</span></span> <span data-ttu-id="88d6b-137">Die Windows-Authentifizierung muss in IIS aktiviert sein, damit `AutomaticAuthentication` funktioniert.</span><span class="sxs-lookup"><span data-stu-id="88d6b-137">Windows Authentication must be enabled in IIS for `AutomaticAuthentication` to function.</span></span> <span data-ttu-id="88d6b-138">Weitere Informationen finden Sie im Thema [Windows-Authentifizierung](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="88d6b-138">For more information, see the [Windows Authentication](xref:security/authentication/windowsauth) topic.</span></span> |
| `AuthenticationDisplayName`    | `null`  | <span data-ttu-id="88d6b-139">Legt den Anzeigename fest, der Benutzern auf Anmeldungsseiten angezeigt wird</span><span class="sxs-lookup"><span data-stu-id="88d6b-139">Sets the display name shown to users on login pages.</span></span> |
| `ForwardClientCertificate`     | `true`  | <span data-ttu-id="88d6b-140">Wenn diese Option `true` ist und der Anforderungsheader `MS-ASPNETCORE-CLIENTCERT` vorhanden ist, wird das `HttpContext.Connection.ClientCertificate` aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="88d6b-140">If `true` and the `MS-ASPNETCORE-CLIENTCERT` request header is present, the `HttpContext.Connection.ClientCertificate` is populated.</span></span> |


### <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="88d6b-141">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="88d6b-141">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="88d6b-142">Die [Middleware für die IIS-Integration](#enable-the-iisintegration-components) und das ASP.NET Core-Modul sind zur Weiterleitung des</span><span class="sxs-lookup"><span data-stu-id="88d6b-142">The [IIS Integration Middleware](#enable-the-iisintegration-components) and the ASP.NET Core Module are configured to forward the:</span></span>

* <span data-ttu-id="88d6b-143">Schemas (HTTP/HTTPS)</span><span class="sxs-lookup"><span data-stu-id="88d6b-143">Scheme (HTTP/HTTPS).</span></span>
* <span data-ttu-id="88d6b-144">und der Remote-IP-Adresse konfiguriert, von der die Anforderung stammt.</span><span class="sxs-lookup"><span data-stu-id="88d6b-144">Remote IP address where the request originated.</span></span>

<span data-ttu-id="88d6b-145">Die [Middleware für die IIS-Integration](#enable-the-iisintegration-components) konfiguriert die Middleware für weitergeleitete Header.</span><span class="sxs-lookup"><span data-stu-id="88d6b-145">The [IIS Integration Middleware](#enable-the-iisintegration-components) configures Forwarded Headers Middleware.</span></span>

<span data-ttu-id="88d6b-146">Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter weiteren Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="88d6b-146">Additional configuration might be required for apps hosted behind additional proxy servers and load balancers.</span></span> <span data-ttu-id="88d6b-147">Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="88d6b-147">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>


### <a name="out-of-process-hosting-model"></a><span data-ttu-id="88d6b-148">Out-of-Process-Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="88d6b-148">Out-of-process hosting model</span></span>

<span data-ttu-id="88d6b-149">Um eine App für Out-of-Process-Hosting zu konfigurieren, legen Sie den Wert der `<AspNetCoreHostingModel>`-Eigenschaft in der Projektdatei ( `.csproj`) auf `OutOfProcess` fest:</span><span class="sxs-lookup"><span data-stu-id="88d6b-149">To configure an app for out-of-process hosting, set the value of the `<AspNetCoreHostingModel>` property to `OutOfProcess` in the project file (`.csproj`):</span></span>

```xml
<PropertyGroup>
  <AspNetCoreHostingModel>OutOfProcess</AspNetCoreHostingModel>
</PropertyGroup>
```

<span data-ttu-id="88d6b-150">Das In-Process-Hosting wird mithilfe von `InProcess` (Standardwert) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="88d6b-150">In-process hosting is set with `InProcess`, which is the default value.</span></span>

<span data-ttu-id="88d6b-151">Beim Wert von `<AspNetCoreHostingModel>` wird die Groß-/Kleinschreibung nicht beachtet, sodass `inprocess` und `outofprocess` gültige Werte sind.</span><span class="sxs-lookup"><span data-stu-id="88d6b-151">The value of `<AspNetCoreHostingModel>` is case insensitive, so `inprocess` and `outofprocess` are valid values.</span></span>

<span data-ttu-id="88d6b-152">Der [Kestrel](xref:fundamentals/servers/kestrel)-Server wird anstelle des IIS-HTTP-Servers (`IISHttpServer`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="88d6b-152">[Kestrel](xref:fundamentals/servers/kestrel) server is used instead of IIS HTTP Server (`IISHttpServer`).</span></span>

<span data-ttu-id="88d6b-153">Bei Out-of-Process ruft [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> auf, um Folgendes zu tun:</span><span class="sxs-lookup"><span data-stu-id="88d6b-153">For out-of-process, [`CreateDefaultBuilder`](xref:fundamentals/host/generic-host#default-builder-settings) calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration%2A> to:</span></span>

* <span data-ttu-id="88d6b-154">Konfigurieren des Ports und des Basispfads, den der Server überwachen soll, wenn er hinter dem ASP.NET Core-Modul ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="88d6b-154">Configure the port and base path the server should listen on when running behind the ASP.NET Core Module.</span></span>
* <span data-ttu-id="88d6b-155">Konfigurieren des Hosts zum Erfassen von Startfehlern.</span><span class="sxs-lookup"><span data-stu-id="88d6b-155">Configure the host to capture startup errors.</span></span>

### <a name="process-name"></a><span data-ttu-id="88d6b-156">Prozessname</span><span class="sxs-lookup"><span data-stu-id="88d6b-156">Process name</span></span>

<span data-ttu-id="88d6b-157">`Process.GetCurrentProcess().ProcessName` meldet `w3wp`/`iisexpress` (In-Process) oder `dotnet` (Out-of-Process).</span><span class="sxs-lookup"><span data-stu-id="88d6b-157">`Process.GetCurrentProcess().ProcessName` reports `w3wp`/`iisexpress` (in-process) or `dotnet` (out-of-process).</span></span>

<span data-ttu-id="88d6b-158">Viele native Module, z.B. die Windows-Authentifizierung, bleiben aktiv.</span><span class="sxs-lookup"><span data-stu-id="88d6b-158">Many native modules, such as Windows Authentication, remain active.</span></span> <span data-ttu-id="88d6b-159">Weitere Informationen zu IIS-Modulen, die im ASP.NET Core-Modul aktiv sind, finden Sie unter <xref:host-and-deploy/iis/modules>.</span><span class="sxs-lookup"><span data-stu-id="88d6b-159">To learn more about IIS modules active with the ASP.NET Core Module, see <xref:host-and-deploy/iis/modules>.</span></span>

<span data-ttu-id="88d6b-160">Das ASP.NET Core-Modul kann außerdem:</span><span class="sxs-lookup"><span data-stu-id="88d6b-160">The ASP.NET Core Module can also:</span></span>

* <span data-ttu-id="88d6b-161">Umgebungsvariablen für den Arbeitsprozess festlegen</span><span class="sxs-lookup"><span data-stu-id="88d6b-161">Set environment variables for the worker process.</span></span>
* <span data-ttu-id="88d6b-162">Die stdout-Ausgabe im Dateispeicher protokollieren, um Probleme beim Start zu beheben</span><span class="sxs-lookup"><span data-stu-id="88d6b-162">Log stdout output to file storage for troubleshooting startup issues.</span></span>
* <span data-ttu-id="88d6b-163">Windows-Authentifizierungstoken weiterleiten</span><span class="sxs-lookup"><span data-stu-id="88d6b-163">Forward Windows authentication tokens.</span></span>
