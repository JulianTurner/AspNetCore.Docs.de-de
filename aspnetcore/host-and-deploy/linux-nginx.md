---
title: Hosten von ASP.NET Core unter Linux mit Nginx
author: rick-anderson
description: Hier finden Sie Informationen zum Einrichten von Nginx als Reverseproxy unter Ubuntu 16.04, um den HTTP-Datenverkehr an eine ASP.NET Core-Web-App weiterzuleiten, die auf Kestrel ausgeführt wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: c4e0d70b41221f272bb4b1fe82cfa531ec6fcf15
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431064"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a><span data-ttu-id="14195-103">Hosten von ASP.NET Core unter Linux mit Nginx</span><span class="sxs-lookup"><span data-stu-id="14195-103">Host ASP.NET Core on Linux with Nginx</span></span>

<span data-ttu-id="14195-104">Von [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="14195-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

<span data-ttu-id="14195-105">In diesem Leitfaden wird das Einrichten einer produktionsbereiten ASP.NET Core-Umgebung auf einem Ubuntu 16.04-Server erläutert.</span><span class="sxs-lookup"><span data-stu-id="14195-105">This guide explains setting up a production-ready ASP.NET Core environment on an Ubuntu 16.04 server.</span></span> <span data-ttu-id="14195-106">Diese Anweisungen sind wahrscheinlich auf neuere Versionen von Ubuntu anwendbar, sie wurden jedoch noch nicht mit neueren Versionen getestet.</span><span class="sxs-lookup"><span data-stu-id="14195-106">These instructions likely work with newer versions of Ubuntu, but the instructions haven't been tested with newer versions.</span></span>

<span data-ttu-id="14195-107">Weitere Informationen zu anderen Linux-Distributionen, die von ASP.NET Core unterstützt werden, finden Sie unter [Voraussetzungen für .NET Core unter Linux](/dotnet/core/linux-prerequisites).</span><span class="sxs-lookup"><span data-stu-id="14195-107">For information on other Linux distributions supported by ASP.NET Core, see [Prerequisites for .NET Core on Linux](/dotnet/core/linux-prerequisites).</span></span>

> [!NOTE]
> <span data-ttu-id="14195-108">Für Ubuntu 14.04 wird `supervisord` für die Überwachung des Kestrel-Prozesses empfohlen.</span><span class="sxs-lookup"><span data-stu-id="14195-108">For Ubuntu 14.04, `supervisord` is recommended as a solution for monitoring the Kestrel process.</span></span> <span data-ttu-id="14195-109">`systemd` ist unter Ubuntu 14.04 nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="14195-109">`systemd` isn't available on Ubuntu 14.04.</span></span> <span data-ttu-id="14195-110">Anweisungen zu Ubuntu 14.04 finden Sie in der [vorherigen Version dieses Themas](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span><span class="sxs-lookup"><span data-stu-id="14195-110">For Ubuntu 14.04 instructions, see the [previous version of this topic](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).</span></span>

<span data-ttu-id="14195-111">In diesem Leitfaden:</span><span class="sxs-lookup"><span data-stu-id="14195-111">This guide:</span></span>

* <span data-ttu-id="14195-112">Wird eine bestehende ASP.NET Core-App hinter einem Reverseproxyserver eingefügt.</span><span class="sxs-lookup"><span data-stu-id="14195-112">Places an existing ASP.NET Core app behind a reverse proxy server.</span></span>
* <span data-ttu-id="14195-113">Wird der Reverseproxyserver eingerichtet, um Anforderungen an den Kestrel-Webserver weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="14195-113">Sets up the reverse proxy server to forward requests to the Kestrel web server.</span></span>
* <span data-ttu-id="14195-114">Wird sichergestellt, dass die Web-App beim Start als Daemon ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="14195-114">Ensures the web app runs on startup as a daemon.</span></span>
* <span data-ttu-id="14195-115">Wird ein Prozessverwaltungstool konfiguriert, das die Web-App beim Neustarten unterstützt.</span><span class="sxs-lookup"><span data-stu-id="14195-115">Configures a process management tool to help restart the web app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="14195-116">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="14195-116">Prerequisites</span></span>

1. <span data-ttu-id="14195-117">Greifen Sie auf einen Ubuntu 16.04-Server mit einem Standardbenutzerkonto mit sudo-Berechtigung zu.</span><span class="sxs-lookup"><span data-stu-id="14195-117">Access to an Ubuntu 16.04 server with a standard user account with sudo privilege.</span></span>
1. <span data-ttu-id="14195-118">Installieren Sie die .NET Core-Runtime auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="14195-118">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="14195-119">Besuchen Sie die [.NET Core-Downloadseite](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="14195-119">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="14195-120">Wählen Sie die neueste Version von .NET Core aus, die keine Vorschauversion ist.</span><span class="sxs-lookup"><span data-stu-id="14195-120">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="14195-121">Laden Sie die neueste Runtime aus der Tabelle unter **Run apps - Runtime** (App-Ausführung – Runtime) herunter, bei der es sich nicht um eine Vorschauversion handelt.</span><span class="sxs-lookup"><span data-stu-id="14195-121">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="14195-122">Klicken Sie auf den Link zu den **Anweisungen zum Linux-Paket-Manager**, und führen Sie die Ubuntu-Anweisungen zu Ihrer Version von Ubuntu aus.</span><span class="sxs-lookup"><span data-stu-id="14195-122">Select the Linux **Package manager instructions** link and follow the Ubuntu instructions for your version of Ubuntu.</span></span>
1. <span data-ttu-id="14195-123">Eine vorhandene ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="14195-123">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="14195-124">Starten Sie die vom Server gehosteten ASP.NET Core-Apps zu einem beliebigen Zeitpunkt nach dem Upgrade des freigegebenen Frameworks neu.</span><span class="sxs-lookup"><span data-stu-id="14195-124">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="14195-125">Veröffentlichen und Kopieren der App</span><span class="sxs-lookup"><span data-stu-id="14195-125">Publish and copy over the app</span></span>

<span data-ttu-id="14195-126">Konfigurieren Sie die App für eine [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="14195-126">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="14195-127">Wenn die App lokal ausgeführt wird und nicht so konfiguriert wurde, dass sie sichere Verbindungen (HTTPS) herstellt, können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="14195-127">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="14195-128">Konfigurieren der App, sodass diese sichere lokale Verbindungen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="14195-128">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="14195-129">Weitere Informationen finden Sie im Abschnitt [HTTPS-Konfiguration](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="14195-129">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="14195-130">Entfernen Sie `https://localhost:5001` (falls vorhanden) aus der `applicationUrl`-Eigenschaft in der Datei `Properties/launchSettings.json`.</span><span class="sxs-lookup"><span data-stu-id="14195-130">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the `Properties/launchSettings.json` file.</span></span>

<span data-ttu-id="14195-131">Führen Sie in der Entwicklungsumgebung [dotnet publish](/dotnet/core/tools/dotnet-publish) aus, um eine App in ein Verzeichnis zu packen (z. B. `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, wobei der Platzhalter `{TARGET FRAMEWORK MONIKER}` der TFM [Target Framework Moniker, Zielframeworkmoniker] ist), das auf dem Server ausgeführt werden kann:</span><span class="sxs-lookup"><span data-stu-id="14195-131">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, where the placeholder `{TARGET FRAMEWORK MONIKER}` is the Target Framework Moniker/TFM) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="14195-132">Die App kann auch als eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) veröffentlicht werden, wenn Sie die .NET Core-Runtime nicht auf dem Server verwalten möchten.</span><span class="sxs-lookup"><span data-stu-id="14195-132">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="14195-133">Kopieren Sie die ASP.NET Core-App auf den Server, indem Sie ein beliebiges Tool verwenden, das in den Workflow der Organisation integriert ist (z. B. `SCP` oder `SFTP`).</span><span class="sxs-lookup"><span data-stu-id="14195-133">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, `SCP`, `SFTP`).</span></span> <span data-ttu-id="14195-134">Web-Apps befinden sich üblicherweise im Verzeichnis `var` (z.B. `var/www/helloapp`).</span><span class="sxs-lookup"><span data-stu-id="14195-134">It's common to locate web apps under the `var` directory (for example, `var/www/helloapp`).</span></span>

> [!NOTE]
> <span data-ttu-id="14195-135">In einem Szenario für die Bereitstellung in der Produktion übernimmt ein Continuous Integration-Workflow die Veröffentlichung der App und das Kopieren der Objekte auf den Server.</span><span class="sxs-lookup"><span data-stu-id="14195-135">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

<span data-ttu-id="14195-136">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="14195-136">Test the app:</span></span>

1. <span data-ttu-id="14195-137">Führen Sie die App über die Befehlszeile aus: `dotnet <app_assembly>.dll`.</span><span class="sxs-lookup"><span data-stu-id="14195-137">From the command line, run the app: `dotnet <app_assembly>.dll`.</span></span>
1. <span data-ttu-id="14195-138">Navigieren Sie in einem Browser zu `http://<serveraddress>:<port>`, und überprüfen Sie, ob die App lokal unter Linux funktioniert.</span><span class="sxs-lookup"><span data-stu-id="14195-138">In a browser, navigate to `http://<serveraddress>:<port>` to verify the app works on Linux locally.</span></span>

## <a name="configure-a-reverse-proxy-server"></a><span data-ttu-id="14195-139">Konfigurieren eines Reverseproxyservers</span><span class="sxs-lookup"><span data-stu-id="14195-139">Configure a reverse proxy server</span></span>

<span data-ttu-id="14195-140">Ein Reverseproxy wird im Allgemeinen zur Unterstützung dynamischer Web-Apps eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="14195-140">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="14195-141">Ein Reverseproxy beendet die HTTP-Anforderung und leitet diese an die ASP.NET Core-App weiter.</span><span class="sxs-lookup"><span data-stu-id="14195-141">A reverse proxy terminates the HTTP request and forwards it to the ASP.NET Core app.</span></span>

### <a name="use-a-reverse-proxy-server"></a><span data-ttu-id="14195-142">Verwenden eines Reverseproxyservers</span><span class="sxs-lookup"><span data-stu-id="14195-142">Use a reverse proxy server</span></span>

<span data-ttu-id="14195-143">Kestrel eignet sich hervorragend für die Bereitstellung dynamischer Inhalte aus ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="14195-143">Kestrel is great for serving dynamic content from ASP.NET Core.</span></span> <span data-ttu-id="14195-144">Die Webbereitstellungsfunktionen sind jedoch nicht so umfangreich wie bei Servern wie IIS, Apache oder Nginx.</span><span class="sxs-lookup"><span data-stu-id="14195-144">However, the web serving capabilities aren't as feature rich as servers such as IIS, Apache, or Nginx.</span></span> <span data-ttu-id="14195-145">Ein Reverseproxyserver kann Arbeiten wie das Verarbeiten von statischen Inhalten, das Zwischenspeichern und Komprimieren von Anforderungen und das Beenden von HTTPS vom HTTP-Server auslagern.</span><span class="sxs-lookup"><span data-stu-id="14195-145">A reverse proxy server can offload work such as serving static content, caching requests, compressing requests, and HTTPS termination from the HTTP server.</span></span> <span data-ttu-id="14195-146">Ein Reverseproxyserver kann sich auf einem dedizierten Computer befinden oder zusammen mit einem HTTP-Server bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="14195-146">A reverse proxy server may reside on a dedicated machine or may be deployed alongside an HTTP server.</span></span>

<span data-ttu-id="14195-147">Für diesen Leitfaden wird eine einzelne Instanz von Nginx verwendet.</span><span class="sxs-lookup"><span data-stu-id="14195-147">For the purposes of this guide, a single instance of Nginx is used.</span></span> <span data-ttu-id="14195-148">Diese wird auf demselben Server ausgeführt, zusammen mit dem HTTP-Server.</span><span class="sxs-lookup"><span data-stu-id="14195-148">It runs on the same server, alongside the HTTP server.</span></span> <span data-ttu-id="14195-149">Je nach Anforderungen kann ein anderes Setup ausgewählt werden.</span><span class="sxs-lookup"><span data-stu-id="14195-149">Based on requirements, a different setup may be chosen.</span></span>

<span data-ttu-id="14195-150">Da Anforderungen vom Reverseproxy weitergeleitet werden, verwenden Sie die [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) im Paket [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides).</span><span class="sxs-lookup"><span data-stu-id="14195-150">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides) package.</span></span> <span data-ttu-id="14195-151">Diese Middleware aktualisiert `Request.Scheme` mithilfe des `X-Forwarded-Proto`-Headers, sodass Umleitungs-URIs und andere Sicherheitsrichtlinien ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="14195-151">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

<span data-ttu-id="14195-152">Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> am Anfang von `Startup.Configure` auf, bevor Sie andere Middleware aufrufen.</span><span class="sxs-lookup"><span data-stu-id="14195-152">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="14195-153">Konfigurieren Sie die Middleware so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` weitergeleitet werden:</span><span class="sxs-lookup"><span data-stu-id="14195-153">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="14195-154">Wenn für die Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> angegeben sind, lauten die weiterzuleitenden Standardheader `None`.</span><span class="sxs-lookup"><span data-stu-id="14195-154">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="14195-155">Proxys, die unter Loopbackadressen (`127.0.0.0/8`, `[::1]`) ausgeführt werden, einschließlich der standardmäßigen Adresse von localhost (`127.0.0.1`), werden standardmäßig als vertrauenswürdig eingestuft.</span><span class="sxs-lookup"><span data-stu-id="14195-155">Proxies running on loopback addresses (`127.0.0.0/8`, `[::1]`), including the standard localhost address (`127.0.0.1`), are trusted by default.</span></span> <span data-ttu-id="14195-156">Wenn andere vertrauenswürdige Proxys oder Netzwerke innerhalb des Unternehmens Anforderungen zwischen dem Internet und dem Webserver verarbeiten, fügen Sie diese der Liste der <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> oder <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> hinzu.</span><span class="sxs-lookup"><span data-stu-id="14195-156">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="14195-157">Das folgende Beispiel fügt einen vertrauenswürdigen Proxyserver mit der IP-Adresse 10.0.0.0.100 der Middleware für weitergeleitete Header `KnownProxies` in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="14195-157">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="14195-158">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="14195-158">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-nginx"></a><span data-ttu-id="14195-159">Installieren von Nginx</span><span class="sxs-lookup"><span data-stu-id="14195-159">Install Nginx</span></span>

<span data-ttu-id="14195-160">Verwenden Sie `apt-get` zum Installieren von Nginx.</span><span class="sxs-lookup"><span data-stu-id="14195-160">Use `apt-get` to install Nginx.</span></span> <span data-ttu-id="14195-161">Das Installationsprogramm erstellt ein `systemd`-Initialisierungsskript, das Nginx beim Systemstart als Dämon ausführt.</span><span class="sxs-lookup"><span data-stu-id="14195-161">The installer creates a `systemd` init script that runs Nginx as daemon on system startup.</span></span> <span data-ttu-id="14195-162">Befolgen Sie die Installationsanleitungen für Ubuntu auf [NGINX: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span><span class="sxs-lookup"><span data-stu-id="14195-162">Follow the installation instructions for Ubuntu at [Nginx: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).</span></span>

> [!NOTE]
> <span data-ttu-id="14195-163">Wenn optionale Nginx-Module benötigt werden, kann die Erstellung von Nginx aus der Quelle erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="14195-163">If optional Nginx modules are required, building Nginx from source might be required.</span></span>

<span data-ttu-id="14195-164">Da Nginx zum ersten Mal installiert wurde, starten Sie es explizit, indem Sie Folgendes ausführen:</span><span class="sxs-lookup"><span data-stu-id="14195-164">Since Nginx was installed for the first time, explicitly start it by running:</span></span>

```bash
sudo service nginx start
```

<span data-ttu-id="14195-165">Stellen Sie sicher, dass ein Browser die Standardangebotsseite für Nginx anzeigt.</span><span class="sxs-lookup"><span data-stu-id="14195-165">Verify a browser displays the default landing page for Nginx.</span></span> <span data-ttu-id="14195-166">Die Landing Page ist unter `http://<server_IP_address>/index.nginx-debian.html` erreichbar.</span><span class="sxs-lookup"><span data-stu-id="14195-166">The landing page is reachable at `http://<server_IP_address>/index.nginx-debian.html`.</span></span>

### <a name="configure-nginx"></a><span data-ttu-id="14195-167">Konfigurieren von Nginx</span><span class="sxs-lookup"><span data-stu-id="14195-167">Configure Nginx</span></span>

<span data-ttu-id="14195-168">Ändern Sie `/etc/nginx/sites-available/default`, um Nginx als Reverseproxy für das Weiterleiten von Anforderungen zu Ihrer ASP.NET Core-App zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="14195-168">To configure Nginx as a reverse proxy to forward HTTP requests to your ASP.NET Core app, modify `/etc/nginx/sites-available/default`.</span></span> <span data-ttu-id="14195-169">Öffnen Sie die Datei in einem Text-Editor und ersetzen Sie den Inhalt durch den Folgendes:</span><span class="sxs-lookup"><span data-stu-id="14195-169">Open it in a text editor, and replace the contents with the following:</span></span>

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

<span data-ttu-id="14195-170">Wenn es sich bei der App um eine SignalR- oder Blazor Server-App handelt, finden Sie jeweils unter <xref:signalr/scale#linux-with-nginx> und <xref:blazor/host-and-deploy/server#linux-with-nginx> weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="14195-170">If the app is a SignalR or Blazor Server app see <xref:signalr/scale#linux-with-nginx> and <xref:blazor/host-and-deploy/server#linux-with-nginx> respectively for more information.</span></span>

<span data-ttu-id="14195-171">Wenn keine Übereinstimmung mit `server_name` gefunden wird, verwendet Nginx den Standardserver.</span><span class="sxs-lookup"><span data-stu-id="14195-171">When no `server_name` matches, Nginx uses the default server.</span></span> <span data-ttu-id="14195-172">Wenn kein Server definiert ist, ist der erste Server in der Konfigurationsdatei der Standardserver.</span><span class="sxs-lookup"><span data-stu-id="14195-172">If no default server is defined, the first server in the configuration file is the default server.</span></span> <span data-ttu-id="14195-173">Als bewährte Methode gilt, einen bestimmten Standardserver hinzuzufügen, der den Statuscode 444 in Ihrer Konfigurationsdatei zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="14195-173">As a best practice, add a specific default server which returns a status code of 444 in your configuration file.</span></span> <span data-ttu-id="14195-174">Im Folgenden wird ein Beispiel für eine Standardserverkonfiguration aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="14195-174">A default server configuration example is:</span></span>

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

<span data-ttu-id="14195-175">Mit der vorhergehenden Konfigurationsdatei und dem vorhergehenden Standardserver akzeptiert Nginx den öffentlichen Datenverkehr über Port 80 mit dem Hostheader `example.com` oder `*.example.com`.</span><span class="sxs-lookup"><span data-stu-id="14195-175">With the preceding configuration file and default server, Nginx accepts public traffic on port 80 with host header `example.com` or `*.example.com`.</span></span> <span data-ttu-id="14195-176">Anforderungen, die mit diesen Hosts nicht übereinstimmen, werden nicht an Kestrel weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="14195-176">Requests not matching these hosts won't get forwarded to Kestrel.</span></span> <span data-ttu-id="14195-177">Nginx leitet die übereinstimmenden Anforderungen unter `http://localhost:5000` an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="14195-177">Nginx forwards the matching requests to Kestrel at `http://localhost:5000`.</span></span> <span data-ttu-id="14195-178">Weitere Informationen finden Sie unter [Verarbeitung einer Anforderung mit Nginx](https://nginx.org/docs/http/request_processing.html).</span><span class="sxs-lookup"><span data-stu-id="14195-178">See [How nginx processes a request](https://nginx.org/docs/http/request_processing.html) for more information.</span></span> <span data-ttu-id="14195-179">Informationen zum Ändern der IP-Adresse bzw. des Ports von Kestrel finden Sie unter [Kestrel: Endpoint configuration (Kestrel: Endpunktkonfiguration)](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="14195-179">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="14195-180">Schlägt die Angabe einer ordnungsgemäßen [server_name-Anweisung](https://nginx.org/docs/http/server_names.html) fehlt, ist Ihre App Sicherheitsrisiken ausgesetzt.</span><span class="sxs-lookup"><span data-stu-id="14195-180">Failure to specify a proper [server_name directive](https://nginx.org/docs/http/server_names.html) exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="14195-181">Platzhalterbindungen in untergeordneten Domänen (z.B. `*.example.com`) verursachen kein Sicherheitsrisiko, wenn Sie die gesamte übergeordnete Domäne steuern (im Gegensatz zu `*.com`, das angreifbar ist).</span><span class="sxs-lookup"><span data-stu-id="14195-181">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="14195-182">Weitere Informationen finden Sie unter [rfc7230 im Abschnitt 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="14195-182">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="14195-183">Wenn die Nginx-Konfiguration eingerichtet ist, können Sie zur Überprüfung der Syntax der Konfigurationsdateien `sudo nginx -t` ausführen.</span><span class="sxs-lookup"><span data-stu-id="14195-183">Once the Nginx configuration is established, run `sudo nginx -t` to verify the syntax of the configuration files.</span></span> <span data-ttu-id="14195-184">Wenn der Test der Konfigurationsdatei erfolgreich ist, können Sie durch Ausführen von `sudo nginx -s reload` erzwingen, dass Nginx die Änderungen übernimmt.</span><span class="sxs-lookup"><span data-stu-id="14195-184">If the configuration file test is successful, force Nginx to pick up the changes by running `sudo nginx -s reload`.</span></span>

<span data-ttu-id="14195-185">Gehen Sie wie folgt vor, um die App auf dem Server direkt auszuführen:</span><span class="sxs-lookup"><span data-stu-id="14195-185">To directly run the app on the server:</span></span>

1. <span data-ttu-id="14195-186">Navigieren Sie zum Verzeichnis der App.</span><span class="sxs-lookup"><span data-stu-id="14195-186">Navigate to the app's directory.</span></span>
1. <span data-ttu-id="14195-187">Führen Sie die App `dotnet <app_assembly.dll>` aus, wobei `app_assembly.dll` der Name der Assemblydatei der App ist.</span><span class="sxs-lookup"><span data-stu-id="14195-187">Run the app: `dotnet <app_assembly.dll>`, where `app_assembly.dll` is the assembly file name of the app.</span></span>

<span data-ttu-id="14195-188">Wenn die App auf dem Server ausgeführt wird, über das Internet jedoch nicht reagiert, sollten Sie die Firewall des Servers überprüfen und sicherstellen, dass Port 80 geöffnet ist.</span><span class="sxs-lookup"><span data-stu-id="14195-188">If the app runs on the server but fails to respond over the Internet, check the server's firewall and confirm that port 80 is open.</span></span> <span data-ttu-id="14195-189">Fügen Sie bei Verwendung einer Azure-Ubuntu-VM eine Regel der Netzwerksicherheitsgruppe (NSG) zur Aktivierung des eingehenden Datenverkehrs an Port 80 hinzu.</span><span class="sxs-lookup"><span data-stu-id="14195-189">If using an Azure Ubuntu VM, add a Network Security Group (NSG) rule that enables inbound port 80 traffic.</span></span> <span data-ttu-id="14195-190">Eine Regel für ausgehenden Datenverkehr an Port 80 muss nicht aktiviert werden, da der ausgehende Datenverkehr automatisch gewährt wird, wenn die Regel für den eingehenden Datenverkehr aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="14195-190">There's no need to enable an outbound port 80 rule, as the outbound traffic is automatically granted when the inbound rule is enabled.</span></span>

<span data-ttu-id="14195-191">Nach dem Testen der App können Sie die App mit `Ctrl+C` in der Eingabeaufforderung beenden.</span><span class="sxs-lookup"><span data-stu-id="14195-191">When done testing the app, shut the app down with `Ctrl+C` at the command prompt.</span></span>

## <a name="monitor-the-app"></a><span data-ttu-id="14195-192">Überwachen der App</span><span class="sxs-lookup"><span data-stu-id="14195-192">Monitor the app</span></span>

<span data-ttu-id="14195-193">Der Server ist dafür eingerichtet, Anforderungen an `http://<serveraddress>:80` an die ASP.NET Core-App weiterzuleiten, die unter `http://127.0.0.1:5000` unter Kestrel ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="14195-193">The server is setup to forward requests made to `http://<serveraddress>:80` on to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="14195-194">Nginx wurde jedoch nicht dafür eingerichtet, den Kestrel-Prozess zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="14195-194">However, Nginx isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="14195-195">`systemd` kann für die Erstellung einer Dienstdatei verwendet werden, um die zugrunde liegende Web-App zu starten und zu überwachen.</span><span class="sxs-lookup"><span data-stu-id="14195-195">`systemd` can be used to create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="14195-196">`systemd` ist ein Initialisierungssystem, das viele leistungsstarke Features zum Starten, Beenden und Verwalten von Prozessen bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="14195-196">`systemd` is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span> 

### <a name="create-the-service-file"></a><span data-ttu-id="14195-197">Erstellen der Dienstdatei</span><span class="sxs-lookup"><span data-stu-id="14195-197">Create the service file</span></span>

<span data-ttu-id="14195-198">Erstellen Sie die Dienstdefinitionsdatei:</span><span class="sxs-lookup"><span data-stu-id="14195-198">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="14195-199">Im Folgenden wird ein Beispiel für eine Dienstdatei der App aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="14195-199">The following is an example service file for the app:</span></span>

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="14195-200">Im vorherigen Beispiel wird der Benutzer, der den Dienst verwaltet, durch die Option `User` angegeben.</span><span class="sxs-lookup"><span data-stu-id="14195-200">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="14195-201">Der Benutzer (`www-data`) muss vorhanden und der ordnungsgemäße Besitzer der App-Dateien sein.</span><span class="sxs-lookup"><span data-stu-id="14195-201">The user (`www-data`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="14195-202">Verwenden Sie `TimeoutStopSec`, um die Dauer der Wartezeit bis zum Herunterfahren der App zu konfigurieren, nachdem diese das anfängliche Interruptsignal empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="14195-202">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="14195-203">Wenn die App in diesem Zeitraum nicht heruntergefahren wird, wird SIGKILL ausgegeben, um die App zu beenden.</span><span class="sxs-lookup"><span data-stu-id="14195-203">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="14195-204">Geben Sie den Wert als einheitenlose Sekunden (z.B. `150`), einen Zeitspannenwert (z.B. `2min 30s`) oder `infinity` an, um das Timeout zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="14195-204">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="14195-205">`TimeoutStopSec` hat den Standardwert `DefaultTimeoutStopSec` in der Manager-Konfigurationsdatei (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`).</span><span class="sxs-lookup"><span data-stu-id="14195-205">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`).</span></span> <span data-ttu-id="14195-206">Das Standardtimeout für die meisten Distributionen beträgt 90 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="14195-206">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="14195-207">Linux verfügt über ein Dateisystem, bei dem die Groß-/Kleinschreibung beachtet wird.</span><span class="sxs-lookup"><span data-stu-id="14195-207">Linux has a case-sensitive file system.</span></span> <span data-ttu-id="14195-208">Wenn Sie `ASPNETCORE_ENVIRONMENT` auf `Production` festlegen, wird die Konfigurationsdatei `appsettings.Production.json` und nicht `appsettings.production.json` gesucht.</span><span class="sxs-lookup"><span data-stu-id="14195-208">Setting `ASPNETCORE_ENVIRONMENT` to `Production` results in searching for the configuration file `appsettings.Production.json`, not `appsettings.production.json`.</span></span>

<span data-ttu-id="14195-209">Einige Werte (z.B. SQL-Verbindungszeichenfolgen) müssen mit Escapezeichen versehen werden, damit die Konfigurationsanbieter die Umgebungsvariablen lesen können.</span><span class="sxs-lookup"><span data-stu-id="14195-209">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="14195-210">Mit dem folgenden Befehl können Sie einen ordnungsgemäß mit Escapezeichen versehenen Wert für die Verwendung in der Konfigurationsdatei generieren:</span><span class="sxs-lookup"><span data-stu-id="14195-210">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14195-211">Doppelpunkte (`:`) als Trennzeichen werden in Umgebungsvariablennamen nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="14195-211">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="14195-212">Verwenden Sie statt eines Doppelpunkts zwei Unterstriche (`__`).</span><span class="sxs-lookup"><span data-stu-id="14195-212">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="14195-213">Der [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables) konvertiert jeweils die zwei Unterstriche in einen Doppelpunkt, wenn die Umgebungsvariablen in die Konfiguration gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="14195-213">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="14195-214">Im folgenden Beispiel wird der Verbindungszeichenfolgeschlüssel `ConnectionStrings:DefaultConnection` als `ConnectionStrings__DefaultConnection` in die Dienstdefinitionsdatei platziert:</span><span class="sxs-lookup"><span data-stu-id="14195-214">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="14195-215">Doppelpunkte (`:`) als Trennzeichen werden in Umgebungsvariablennamen nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="14195-215">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="14195-216">Verwenden Sie statt eines Doppelpunkts zwei Unterstriche (`__`).</span><span class="sxs-lookup"><span data-stu-id="14195-216">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="14195-217">Der [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables-configuration-provider) konvertiert jeweils die zwei Unterstriche in einen Doppelpunkt, wenn die Umgebungsvariablen in die Konfiguration gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="14195-217">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="14195-218">Im folgenden Beispiel wird der Verbindungszeichenfolgeschlüssel `ConnectionStrings:DefaultConnection` als `ConnectionStrings__DefaultConnection` in die Dienstdefinitionsdatei platziert:</span><span class="sxs-lookup"><span data-stu-id="14195-218">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="14195-219">Speichern Sie die Datei, und aktivieren Sie den Dienst.</span><span class="sxs-lookup"><span data-stu-id="14195-219">Save the file and enable the service.</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="14195-220">Starten Sie den Dienst, und überprüfen Sie, ob er ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="14195-220">Start the service and verify that it's running.</span></span>

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="14195-221">Wenn der Reverseproxy konfiguriert ist und Kestrel durch `systemd` verwaltet wird, ist die Webanwendung vollständig konfiguriert. Auf diese kann dann über einen Browser auf dem lokalen Computer unter `http://localhost` zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="14195-221">With the reverse proxy configured and Kestrel managed through `systemd`, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="14195-222">Der Zugriff ist auch von einem Remotecomputer aus möglich, sofern keine Firewall diesen blockiert.</span><span class="sxs-lookup"><span data-stu-id="14195-222">It's also accessible from a remote machine, barring any firewall that might be blocking.</span></span> <span data-ttu-id="14195-223">Beim Überprüfen der Antwortheader zeigt der Header `Server` die ASP.NET Core-App an, die von Kestrel verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="14195-223">Inspecting the response headers, the `Server` header shows the ASP.NET Core app being served by Kestrel.</span></span>

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="14195-224">Protokoll anzeigen...</span><span class="sxs-lookup"><span data-stu-id="14195-224">View logs</span></span>

<span data-ttu-id="14195-225">Da die Web-App, die Kestrel verwendet, von `systemd` verwaltet wird, werden alle Ereignisse und Prozesse in einem zentralen Journal protokolliert.</span><span class="sxs-lookup"><span data-stu-id="14195-225">Since the web app using Kestrel is managed using `systemd`, all events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="14195-226">Dieses Journal enthält jedoch alle Einträge für alle Dienste und Prozesse, die von `systemd` verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="14195-226">However, this journal includes all entries for all services and processes managed by `systemd`.</span></span> <span data-ttu-id="14195-227">Verwenden Sie folgenden Befehl, um die `kestrel-helloapp.service`-spezifischen Elemente anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="14195-227">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="14195-228">Für weiteres Filtern können Zeitoptionen wie `--since today`, `--until 1 hour ago` oder eine Kombination aus diesen die Menge der zurückgegebenen Einträge verringern.</span><span class="sxs-lookup"><span data-stu-id="14195-228">For further filtering, time options such as `--since today`, `--until 1 hour ago` or a combination of these can reduce the amount of entries returned.</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="14195-229">Schutz von Daten</span><span class="sxs-lookup"><span data-stu-id="14195-229">Data protection</span></span>

<span data-ttu-id="14195-230">Der [Stapel zum Schutz von Daten in ASP.NET Core](xref:security/data-protection/introduction) wird von mehreren [ASP.NET Core-Middlewares](xref:fundamentals/middleware/index) verwendet. Hierzu gehören die Authentifizierungsmiddleware (zum Beispiel die cookiemiddleware) und Maßnahmen zum Schutz vor websiteübergreifenden Anforderungsfälschungen (CSRF).</span><span class="sxs-lookup"><span data-stu-id="14195-230">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="14195-231">Selbst wenn Datenschutz-APIs nicht im Benutzercode aufgerufen werden, sollte der Schutz von Daten konfiguriert werden, um einen persistenten kryptografischen [Schlüsselspeicher](xref:security/data-protection/implementation/key-management) zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="14195-231">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="14195-232">Wenn der Schutz von Daten nicht konfiguriert ist, werden die Schlüssel beim Neustarten der App im Arbeitsspeicher gespeichert und verworfen.</span><span class="sxs-lookup"><span data-stu-id="14195-232">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="14195-233">Falls der Schlüsselbund im Arbeitsspeicher gespeichert wird, wenn die App neu gestartet wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="14195-233">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="14195-234">Alle cookiebasierten Authentifizierungstoken werden für ungültig erklärt.</span><span class="sxs-lookup"><span data-stu-id="14195-234">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="14195-235">Benutzer müssen sich bei ihrer nächsten Anforderung erneut anmelden.</span><span class="sxs-lookup"><span data-stu-id="14195-235">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="14195-236">Alle mit dem Schlüsselbund geschützte Daten können nicht mehr entschlüsselt werden.</span><span class="sxs-lookup"><span data-stu-id="14195-236">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="14195-237">Dies kann [CSRF-Token](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) und [ASP.NET Core-MVC-TempData-cookies](xref:fundamentals/app-state#tempdata) einschließen.</span><span class="sxs-lookup"><span data-stu-id="14195-237">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="14195-238">Wenn Sie den Schutz von Daten konfigurieren möchten, um den Schlüsselring persistent zu speichern und zu verschlüsseln, finden Sie in den folgenden Artikeln weitere Informationen:</span><span class="sxs-lookup"><span data-stu-id="14195-238">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a><span data-ttu-id="14195-239">Lange Anforderungsheaderfelder</span><span class="sxs-lookup"><span data-stu-id="14195-239">Long request header fields</span></span>

<span data-ttu-id="14195-240">Die Standardeinstellungen für den Proxyserver schränken die Anforderungsheaderfelder in der Regel je nach Plattform auf 4 K oder 8 K ein.</span><span class="sxs-lookup"><span data-stu-id="14195-240">Proxy server default settings typically limit request header fields to 4 K or 8 K depending on the platform.</span></span> <span data-ttu-id="14195-241">Eine App erfordert möglicherweise Felder, die länger als die Standardwerte sind (z. B. Apps, die [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) verwenden).</span><span class="sxs-lookup"><span data-stu-id="14195-241">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="14195-242">Wenn längere Felder erforderlich sind, müssen die Standardeinstellungen des Proxyservers angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="14195-242">If longer fields are required, the proxy server's default settings require adjustment.</span></span> <span data-ttu-id="14195-243">Die anzuwendenden Werte hängen vom jeweiligen Szenario ab.</span><span class="sxs-lookup"><span data-stu-id="14195-243">The values to apply depend on the scenario.</span></span> <span data-ttu-id="14195-244">Weitere Informationen finden Sie in der Dokumentation Ihres Servers.</span><span class="sxs-lookup"><span data-stu-id="14195-244">For more information, see your server's documentation.</span></span>

* [<span data-ttu-id="14195-245">proxy_buffer_size</span><span class="sxs-lookup"><span data-stu-id="14195-245">proxy_buffer_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [<span data-ttu-id="14195-246">proxy_buffers</span><span class="sxs-lookup"><span data-stu-id="14195-246">proxy_buffers</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [<span data-ttu-id="14195-247">proxy_busy_buffers_size</span><span class="sxs-lookup"><span data-stu-id="14195-247">proxy_busy_buffers_size</span></span>](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [<span data-ttu-id="14195-248">large_client_header_buffers</span><span class="sxs-lookup"><span data-stu-id="14195-248">large_client_header_buffers</span></span>](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> <span data-ttu-id="14195-249">Erhöhen Sie die Standardwerte der Proxypuffer nur dann, wenn dies absolut erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="14195-249">Don't increase the default values of proxy buffers unless necessary.</span></span> <span data-ttu-id="14195-250">Ein Erhöhen dieser Werte vergrößert das Risiko von Pufferüberlauf- (Überlauf-) und Denial-of-Service-Angriffen durch böswillige Benutzer.</span><span class="sxs-lookup"><span data-stu-id="14195-250">Increasing these values increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="secure-the-app"></a><span data-ttu-id="14195-251">Sichern der App</span><span class="sxs-lookup"><span data-stu-id="14195-251">Secure the app</span></span>

### <a name="enable-apparmor"></a><span data-ttu-id="14195-252">Aktivieren von AppArmor</span><span class="sxs-lookup"><span data-stu-id="14195-252">Enable AppArmor</span></span>

<span data-ttu-id="14195-253">Bei Linux Security Modules (LSM) handelt es sich um ein Framework, das seit Linux 2.6 Teil des Linux-Kernels ist.</span><span class="sxs-lookup"><span data-stu-id="14195-253">Linux Security Modules (LSM) is a framework that's part of the Linux kernel since Linux 2.6.</span></span> <span data-ttu-id="14195-254">LSM unterstützt verschiedene Implementierungen von Sicherheitsmodulen.</span><span class="sxs-lookup"><span data-stu-id="14195-254">LSM supports different implementations of security modules.</span></span> <span data-ttu-id="14195-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) ist ein LSM, das ein obligatorisches Zugriffssteuerungssystem implementiert, das das Programm auf einen beschränkten Satz von Ressourcen begrenzt.</span><span class="sxs-lookup"><span data-stu-id="14195-255">[AppArmor](https://wiki.ubuntu.com/AppArmor) is a LSM that implements a Mandatory Access Control system which allows confining the program to a limited set of resources.</span></span> <span data-ttu-id="14195-256">Versichern Sie sich, dass AppArmor aktiviert und ordnungsgemäß konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="14195-256">Ensure AppArmor is enabled and properly configured.</span></span>

### <a name="configure-the-firewall"></a><span data-ttu-id="14195-257">Konfigurieren der Firewall</span><span class="sxs-lookup"><span data-stu-id="14195-257">Configure the firewall</span></span>

<span data-ttu-id="14195-258">Schließen Sie alle externen Ports, die nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="14195-258">Close off all external ports that are not in use.</span></span> <span data-ttu-id="14195-259">„Uncomplicated Firewall“ (UFW) stellt ein Front-End für `iptables` bereit, indem eine CLI zum Konfigurieren der Firewall bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="14195-259">Uncomplicated firewall (ufw) provides a front end for `iptables` by providing a CLI for configuring the firewall.</span></span>

> [!WARNING]
> <span data-ttu-id="14195-260">Eine Firewall verhindert den Zugriff auf das gesamte System, wenn dieses nicht ordnungsgemäß konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="14195-260">A firewall will prevent access to the whole system if not configured correctly.</span></span> <span data-ttu-id="14195-261">Falls Sie SSH zum Verbindungsaufbau verwenden und den falschen SSH-Port angeben, können Sie nicht mehr auf das System zugreifen.</span><span class="sxs-lookup"><span data-stu-id="14195-261">Failure to specify the correct SSH port will effectively lock you out of the system if you are using SSH to connect to it.</span></span> <span data-ttu-id="14195-262">Der Standardport ist 22.</span><span class="sxs-lookup"><span data-stu-id="14195-262">The default port is 22.</span></span> <span data-ttu-id="14195-263">Weitere Informationen finden Sie unter [introduction to ufw (Einführung in ufw)](https://help.ubuntu.com/community/UFW) und in den [Manpages](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span><span class="sxs-lookup"><span data-stu-id="14195-263">For more information, see the [introduction to ufw](https://help.ubuntu.com/community/UFW) and the [manual](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).</span></span>

<span data-ttu-id="14195-264">Installieren Sie `ufw`, und konfigurieren Sie das Tool so, dass der Datenverkehr auf allen erforderlich Ports zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="14195-264">Install `ufw` and configure it to allow traffic on any ports needed.</span></span>

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a><span data-ttu-id="14195-265">Sichern von Nginx</span><span class="sxs-lookup"><span data-stu-id="14195-265">Secure Nginx</span></span>

#### <a name="change-the-nginx-response-name"></a><span data-ttu-id="14195-266">Ändern des Namens der Nginx-Antwort</span><span class="sxs-lookup"><span data-stu-id="14195-266">Change the Nginx response name</span></span>

<span data-ttu-id="14195-267">Bearbeiten Sie `src/http/ngx_http_header_filter_module.c`:</span><span class="sxs-lookup"><span data-stu-id="14195-267">Edit `src/http/ngx_http_header_filter_module.c`:</span></span>

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a><span data-ttu-id="14195-268">Konfigurieren von Optionen</span><span class="sxs-lookup"><span data-stu-id="14195-268">Configure options</span></span>

<span data-ttu-id="14195-269">Konfigurieren Sie den Server mit zusätzlichen erforderlichen Modulen.</span><span class="sxs-lookup"><span data-stu-id="14195-269">Configure the server with additional required modules.</span></span> <span data-ttu-id="14195-270">Erwägen Sie zum Schutz der App die Verwendung einer Web-App-Firewall wie z.B. [ModSecurity](https://www.modsecurity.org/).</span><span class="sxs-lookup"><span data-stu-id="14195-270">Consider using a web app firewall, such as [ModSecurity](https://www.modsecurity.org/), to harden the app.</span></span>

#### <a name="https-configuration"></a><span data-ttu-id="14195-271">HTTPS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="14195-271">HTTPS configuration</span></span>

<span data-ttu-id="14195-272">**Konfigurieren der App für sichere (HTTPS) lokale Verbindungen**</span><span class="sxs-lookup"><span data-stu-id="14195-272">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="14195-273">Der [dotnet run](/dotnet/core/tools/dotnet-run)-Befehl verwendet die `Properties/launchSettings.json`-Datei der App, die die App so konfiguriert, dass diese an den URLs lauscht, die von der `applicationUrl`-Eigenschaft bereitgestellt werden, z. B. `https://localhost:5001;http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="14195-273">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's `Properties/launchSettings.json` file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="14195-274">Konfigurieren Sie mithilfe eines der folgenden Ansätze die App so, dass sie bei der Entwicklung für den Befehl `dotnet run` oder die Entwicklungsumgebung (<kbd>F5</kbd> oder <kbd>STRG</kbd>+<kbd>F5</kbd> in Visual Studio Code) ein Zertifikat verwendet:</span><span class="sxs-lookup"><span data-stu-id="14195-274">Configure the app to use a certificate in development for the `dotnet run` command or development environment (<kbd>F5</kbd> or <kbd>Ctrl</kbd>+<kbd>F5</kbd> in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="14195-275">[Ersetzen des Standardzertifikats aus der Konfiguration](xref:fundamentals/servers/kestrel#configuration) (*Empfohlen*)</span><span class="sxs-lookup"><span data-stu-id="14195-275">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="14195-276">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="14195-276">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="14195-277">**Konfigurieren des Reverseproxys für sichere (HTTPS) Clientverbindungen**</span><span class="sxs-lookup"><span data-stu-id="14195-277">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

* <span data-ttu-id="14195-278">Konfigurieren Sie den Server, damit dieser für den HTTPS-Datenverkehr an Port `443` empfangsbereit ist, indem Sie ein gültiges Zertifikat angeben, das von einer vertrauenswürdigen Zertifizierungsstelle (Certificate Authority, CA) ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="14195-278">Configure the server to listen to HTTPS traffic on port `443` by specifying a valid certificate issued by a trusted Certificate Authority (CA).</span></span>

* <span data-ttu-id="14195-279">Stärken Sie Ihre Sicherheit, indem Sie einige der in der folgenden Datei ( `/etc/nginx/nginx.conf`) dargestellten Methoden verwenden.</span><span class="sxs-lookup"><span data-stu-id="14195-279">Harden the security by employing some of the practices depicted in the following `/etc/nginx/nginx.conf` file.</span></span> <span data-ttu-id="14195-280">Die Beispiele schließen das Auswählen einer stärkeren Verschlüsselung und das Weiterleiten allen Datenverkehrs über HTTP auf HTTPS ein.</span><span class="sxs-lookup"><span data-stu-id="14195-280">Examples include choosing a stronger cipher and redirecting all traffic over HTTP to HTTPS.</span></span>

  > [!NOTE]
  > <span data-ttu-id="14195-281">Für Entwicklungsumgebungen empfehlen sich temporäre Umleitungen (302) anstelle permanenter Umleitungen (301).</span><span class="sxs-lookup"><span data-stu-id="14195-281">For development environments, we recommend using temporary redirects (302) rather than permanent redirects (301).</span></span> <span data-ttu-id="14195-282">Das Zwischenspeichern von Links kann in Entwicklungsumgebungen zu instabilem Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="14195-282">Link caching can cause unstable behavior in development environments.</span></span>

* <span data-ttu-id="14195-283">Durch das Hinzufügen eines `HTTP Strict-Transport-Security`-Headers (HSTS) wird sichergestellt, dass alle nachfolgenden Anforderungen vom Client über HTTPS erfolgen.</span><span class="sxs-lookup"><span data-stu-id="14195-283">Adding an `HTTP Strict-Transport-Security` (HSTS) header ensures all subsequent requests made by the client are over HTTPS.</span></span>

  <span data-ttu-id="14195-284">Wichtige Anleitungen zu HSTS finden Sie unter <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span><span class="sxs-lookup"><span data-stu-id="14195-284">For important guidance on HSTS, see <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.</span></span>

* <span data-ttu-id="14195-285">Wenn HTTPS in Zukunft deaktiviert wird, halten Sie sich an einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="14195-285">If HTTPS will be disabled in the future, use one of the following approaches:</span></span>

  * <span data-ttu-id="14195-286">Fügen Sie den HSTS-Header nicht hinzu.</span><span class="sxs-lookup"><span data-stu-id="14195-286">Don't add the HSTS header.</span></span>
  * <span data-ttu-id="14195-287">Wählen Sie einen kurzen `max-age`-Wert aus.</span><span class="sxs-lookup"><span data-stu-id="14195-287">Choose a short `max-age` value.</span></span>

<span data-ttu-id="14195-288">Fügen Sie die Konfigurationsdatei `/etc/nginx/proxy.conf` hinzu:</span><span class="sxs-lookup"><span data-stu-id="14195-288">Add the `/etc/nginx/proxy.conf` configuration file:</span></span>

[!code-nginx[](linux-nginx/proxy.conf)]

<span data-ttu-id="14195-289">**Ersetzen** Sie den Inhalt der Konfigurationsdatei `/etc/nginx/nginx.conf` durch die folgende Datei.</span><span class="sxs-lookup"><span data-stu-id="14195-289">**Replace** the contents of the `/etc/nginx/nginx.conf` configuration file with the following file.</span></span> <span data-ttu-id="14195-290">Das Beispiel enthält die Abschnitte `http` und `server` in einer Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="14195-290">The example contains both `http` and `server` sections in one configuration file.</span></span>

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> <span data-ttu-id="14195-291">Blazor WebAssembly-Apps erfordern einen größeren `burst`-Parameterwert, damit die größere Anzahl von Anforderungen verarbeitet werden kann, die von einer App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="14195-291">Blazor WebAssembly apps require a larger `burst` parameter value to accommodate the larger number of requests made by an app.</span></span> <span data-ttu-id="14195-292">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/webassembly#nginx>.</span><span class="sxs-lookup"><span data-stu-id="14195-292">For more information, see <xref:blazor/host-and-deploy/webassembly#nginx>.</span></span>

#### <a name="secure-nginx-from-clickjacking"></a><span data-ttu-id="14195-293">Sichern von Nginx vor Clickjacking</span><span class="sxs-lookup"><span data-stu-id="14195-293">Secure Nginx from clickjacking</span></span>

<span data-ttu-id="14195-294">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), auch bekannt als *UI Redress-Angriff*, ist ein böswilliger Angriff, bei dem ein Websitebesucher dazu verleitet wird, auf einen Link oder eine Schaltfläche zu klicken, der bzw. die sich auf einer anderen Seite als der aktuell besuchten Seite befindet.</span><span class="sxs-lookup"><span data-stu-id="14195-294">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="14195-295">Verwenden Sie `X-FRAME-OPTIONS` zum Sichern der Website.</span><span class="sxs-lookup"><span data-stu-id="14195-295">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="14195-296">So wehren Sie Clickjacking-Angriffe ab:</span><span class="sxs-lookup"><span data-stu-id="14195-296">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="14195-297">Bearbeiten Sie die Datei `nginx.conf`:</span><span class="sxs-lookup"><span data-stu-id="14195-297">Edit the `nginx.conf` file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="14195-298">Fügen Sie die Zeile `add_header X-Frame-Options "SAMEORIGIN";` hinzu:</span><span class="sxs-lookup"><span data-stu-id="14195-298">Add the line: `add_header X-Frame-Options "SAMEORIGIN";`</span></span>

1. <span data-ttu-id="14195-299">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="14195-299">Save the file.</span></span>
1. <span data-ttu-id="14195-300">Starten Sie Nginx neu.</span><span class="sxs-lookup"><span data-stu-id="14195-300">Restart Nginx.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="14195-301">MIME-Typermittlung</span><span class="sxs-lookup"><span data-stu-id="14195-301">MIME-type sniffing</span></span>

<span data-ttu-id="14195-302">Dieser Header hindert die meisten Browser an der MIME-Ermittlung einer Antwort vom deklarierten Inhaltstyp weg, da der Header den Browser anweist, den Inhaltstyp der Antwort nicht zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="14195-302">This header prevents most browsers from MIME-sniffing a response away from the declared content type, as the header instructs the browser not to override the response content type.</span></span> <span data-ttu-id="14195-303">Durch die Option `nosniff` wird der Inhalt vom Browser als `text/html` gerendert, wenn der Server sagt, dass es sich bei diesem um `text/html` handelt.</span><span class="sxs-lookup"><span data-stu-id="14195-303">With the `nosniff` option, if the server says the content is `text/html`, the browser renders it as `text/html`.</span></span>

1. <span data-ttu-id="14195-304">Bearbeiten Sie die Datei `nginx.conf`:</span><span class="sxs-lookup"><span data-stu-id="14195-304">Edit the `nginx.conf` file:</span></span>

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   <span data-ttu-id="14195-305">Fügen Sie die Zeile `add_header X-Content-Type-Options "nosniff";` hinzu:</span><span class="sxs-lookup"><span data-stu-id="14195-305">Add the line: `add_header X-Content-Type-Options "nosniff";`</span></span>

1. <span data-ttu-id="14195-306">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="14195-306">Save the file.</span></span>
1. <span data-ttu-id="14195-307">Starten Sie Nginx neu.</span><span class="sxs-lookup"><span data-stu-id="14195-307">Restart Nginx.</span></span>

## <a name="additional-nginx-suggestions"></a><span data-ttu-id="14195-308">Zusätzliche Vorschläge zu Nginx</span><span class="sxs-lookup"><span data-stu-id="14195-308">Additional Nginx suggestions</span></span>

<span data-ttu-id="14195-309">Starten Sie die vom Server gehosteten ASP.NET Core-Apps nach dem Upgrade des freigegebenen Frameworks neu.</span><span class="sxs-lookup"><span data-stu-id="14195-309">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14195-310">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="14195-310">Additional resources</span></span>

* [<span data-ttu-id="14195-311">Voraussetzungen für .NET Core unter Linux</span><span class="sxs-lookup"><span data-stu-id="14195-311">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* [<span data-ttu-id="14195-312">Nginx: Binary Releases: Official Debian/Ubuntu packages</span><span class="sxs-lookup"><span data-stu-id="14195-312">Nginx: Binary Releases: Official Debian/Ubuntu packages</span></span>](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="14195-313">NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)</span><span class="sxs-lookup"><span data-stu-id="14195-313">NGINX: Using the Forwarded header</span></span>](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
