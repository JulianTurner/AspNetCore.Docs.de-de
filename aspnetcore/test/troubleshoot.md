---
title: Problembehandlung und Debuggen von ASP.NET Core-Projekten
author: Rick-Anderson
description: In diesem Artikel werden Warnungen und Fehler erläutert. Außerdem erfahren Sie, wie die Problembehandlung in ASP.NET Core-Projekten funktioniert.
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
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
uid: test/troubleshoot
ms.openlocfilehash: 8e6c640cd775e5d4cbe6e34c1cecc391baf57344
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059571"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="66f4c-103">Problembehandlung und Debuggen von ASP.NET Core-Projekten</span><span class="sxs-lookup"><span data-stu-id="66f4c-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="66f4c-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="66f4c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="66f4c-105">Die folgenden Links bieten Hinweise zur Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="66f4c-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="66f4c-106">NDC Conference (London, 2018): Diagnostizieren von Problemen in ASP.NET Core-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="66f4c-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="66f4c-107">ASP.NET-Blog: Problembehandlung bei ASP. NET Core-Leistungsproblemen</span><span class="sxs-lookup"><span data-stu-id="66f4c-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="66f4c-108">.NET Core SDK-Warnungen</span><span class="sxs-lookup"><span data-stu-id="66f4c-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="66f4c-109">Sowohl die 32-Bit- als auch die 64-Bit-Version des .NET Core SDKs sind installiert.</span><span class="sxs-lookup"><span data-stu-id="66f4c-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="66f4c-110">Im Dialogfeld **Neues Projekt** für ASP.NET Core wird möglicherweise die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="66f4c-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="66f4c-111">Sowohl 32-Bit- als auch 64-Bit-Versionen des .NET Core SDKs sind installiert.</span><span class="sxs-lookup"><span data-stu-id="66f4c-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="66f4c-112">Es werden nur Vorlagen der 64-Bit-Versionen angezeigt, die unter „C:\\Programme\\dotnet\\sdk\\“ installiert sind.</span><span class="sxs-lookup"><span data-stu-id="66f4c-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="66f4c-113">Diese Warnung wird angezeigt, wenn sowohl 32-Bit- (x86) als auch 64-Bit-Versionen (x64) des [.NET Core SDKs](https://dotnet.microsoft.com/download/dotnet-core) installiert sind.</span><span class="sxs-lookup"><span data-stu-id="66f4c-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="66f4c-114">Häufige Gründe für die Installation beider Versionen sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="66f4c-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="66f4c-115">Sie haben das .NET Core SDK-Installationsprogramm ursprünglich mit einem 32-Bit-Computer heruntergeladen, es dann aber auf einen 64-Bit-Computer kopiert und installiert.</span><span class="sxs-lookup"><span data-stu-id="66f4c-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="66f4c-116">Die 32-Bit-Version des .NET Core SDKs wurde von einer anderen Anwendung installiert.</span><span class="sxs-lookup"><span data-stu-id="66f4c-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="66f4c-117">Die falsche Version wurde heruntergeladen und installiert.</span><span class="sxs-lookup"><span data-stu-id="66f4c-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="66f4c-118">Deinstallieren Sie die 32-Bit-Version des .NET Core SDKs, um diese Warnung zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="66f4c-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="66f4c-119">Deinstallieren Sie über **Systemsteuerung** > **Programme und Features** > **Programm deinstallieren oder ändern**.</span><span class="sxs-lookup"><span data-stu-id="66f4c-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="66f4c-120">Wenn Sie verstehen, warum die Warnung und ihre Auswirkungen auftreten, können Sie die Warnung ignorieren.</span><span class="sxs-lookup"><span data-stu-id="66f4c-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="66f4c-121">Das .NET Core SDK ist an mehreren Speicherorten installiert.</span><span class="sxs-lookup"><span data-stu-id="66f4c-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="66f4c-122">Im Dialogfeld **Neues Projekt** für ASP.NET Core wird möglicherweise die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="66f4c-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="66f4c-123">Das .NET Core SDK ist an mehreren Speicherorten installiert.</span><span class="sxs-lookup"><span data-stu-id="66f4c-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="66f4c-124">Es werden nur Vorlagen des SDKs angezeigt, die unter „C:\\Programme\\dotnet\\sdk\\“ installiert sind.</span><span class="sxs-lookup"><span data-stu-id="66f4c-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="66f4c-125">Diese Meldung wird angezeigt, wenn Sie über mindestens eine Installation des .NET Core SDK in einem Verzeichnis außerhalb von *C:\\Programme\\dotnet\\sdk\\* verfügen.</span><span class="sxs-lookup"><span data-stu-id="66f4c-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="66f4c-126">Normalerweise geschieht dies, wenn das .NET Core SDKs auf einem Computer über Kopieren/Einfügen anstatt mit dem MSI-Installationsprogramm bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="66f4c-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="66f4c-127">Deinstallieren Sie alle 32-Bit-Versionen des .NET Core SDKs und der Laufzeiten, um diese Warnung zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="66f4c-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="66f4c-128">Deinstallieren Sie über **Systemsteuerung** > **Programme und Features** > **Programm deinstallieren oder ändern**.</span><span class="sxs-lookup"><span data-stu-id="66f4c-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="66f4c-129">Wenn Sie verstehen, warum die Warnung und ihre Auswirkungen auftreten, können Sie die Warnung ignorieren.</span><span class="sxs-lookup"><span data-stu-id="66f4c-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="66f4c-130">Es wurden keine .NET Core SDKs erkannt.</span><span class="sxs-lookup"><span data-stu-id="66f4c-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="66f4c-131">Im Dialogfeld **Neues Projekt** in Visual Studio für ASP.NET Core wird möglicherweise die folgende Warnung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="66f4c-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="66f4c-132">Es wurden keine .NET Core SDKs erkannt. Stellen Sie sicher, dass sie in der Umgebungsvariablen `PATH` enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="66f4c-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="66f4c-133">Wenn Sie einen `dotnet`-Befehl ausführen, wird die Meldung wie folgt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="66f4c-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="66f4c-134">Es konnten keine installierten Dotnet-SDKs gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="66f4c-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="66f4c-135">Diese Warnungen werden angezeigt, wenn die Umgebungsvariable `PATH` nicht auf . NET Core SDKs auf dem Computer verweist.</span><span class="sxs-lookup"><span data-stu-id="66f4c-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="66f4c-136">Dieses Problem lässt sich wie folgt beheben:</span><span class="sxs-lookup"><span data-stu-id="66f4c-136">To resolve this problem:</span></span>

* <span data-ttu-id="66f4c-137">Installieren Sie das .NET Core-SDK.</span><span class="sxs-lookup"><span data-stu-id="66f4c-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="66f4c-138">Das aktuellste Installationsprogramm erhalten Sie unter [.NET-Downloads](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="66f4c-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="66f4c-139">Stellen Sie sicher, dass die Umgebungsvariable `PATH` auf den Speicherort verweist, an dem das SDK installiert ist (`C:\Program Files\dotnet\` für 64-Bit/x64 oder `C:\Program Files (x86)\dotnet\` für 32-Bit/x86).</span><span class="sxs-lookup"><span data-stu-id="66f4c-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="66f4c-140">Das SDK-Installationsprogramm legt normalerweise den `PATH` fest.</span><span class="sxs-lookup"><span data-stu-id="66f4c-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="66f4c-141">Installieren Sie immer SDKs und Laufzeiten mit derselben Bitanzahl auf demselben Computer.</span><span class="sxs-lookup"><span data-stu-id="66f4c-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="66f4c-142">Fehlendes SDK nach der Installation des .NET Core-Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="66f4c-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="66f4c-143">Durch die Installation des [.NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) wird der `PATH` bei der Installation der .NET Core-Laufzeit so geändert, dass er auf die 32-Bit-Version (x86) von .NET Core (`C:\Program Files (x86)\dotnet\`) verweist.</span><span class="sxs-lookup"><span data-stu-id="66f4c-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="66f4c-144">Dies kann zu fehlenden SDKs führen, wenn der 32-Bit (x86) .NET Core-Befehl `dotnet` verwendet wird ([Es wurden keine .NET Core SDKs erkannt](#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="66f4c-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="66f4c-145">Um dieses Problem zu beheben, verschieben Sie `C:\Program Files\dotnet\` an einen Speicherort vor `C:\Program Files (x86)\dotnet\` in dem `PATH`.</span><span class="sxs-lookup"><span data-stu-id="66f4c-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="66f4c-146">Abrufen von Daten aus einer App</span><span class="sxs-lookup"><span data-stu-id="66f4c-146">Obtain data from an app</span></span>

<span data-ttu-id="66f4c-147">Wenn eine App in der Lage ist, auf Anforderungen zu antworten, können Sie die folgenden Daten über eine Middleware aus der App erhalten:</span><span class="sxs-lookup"><span data-stu-id="66f4c-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="66f4c-148">Anforderung: Methode, Schema, Host, Pfadbasis, Pfad, Abfragezeichenfolge, Header</span><span class="sxs-lookup"><span data-stu-id="66f4c-148">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="66f4c-149">Verbindung: Remote-IP-Adresse, Remoteport, lokale IP-Adresse, lokaler Port, Clientzertifikat</span><span class="sxs-lookup"><span data-stu-id="66f4c-149">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="66f4c-150">:::no-loc(Identity):::: Name, Anzeigename</span><span class="sxs-lookup"><span data-stu-id="66f4c-150">:::no-loc(Identity):::: Name, display name</span></span>
* <span data-ttu-id="66f4c-151">Konfigurationseinstellungen</span><span class="sxs-lookup"><span data-stu-id="66f4c-151">Configuration settings</span></span>
* <span data-ttu-id="66f4c-152">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="66f4c-152">Environment variables</span></span>

<span data-ttu-id="66f4c-153">Platzieren Sie den folgenden [Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)-Code an den Anfang der Anforderungsverarbeitungspipeline der `Startup.Configure`-Methode.</span><span class="sxs-lookup"><span data-stu-id="66f4c-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="66f4c-154">Die Umgebung wird vor der Ausführung der Middleware überprüft, um sicherzustellen, dass der Code nur in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="66f4c-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="66f4c-155">Um die Umgebung zu erhalten, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="66f4c-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="66f4c-156">Fügen Sie die `IHostingEnvironment` in die `Startup.Configure`-Methode ein, und prüfen Sie die Umgebung mit der lokalen Variablen.</span><span class="sxs-lookup"><span data-stu-id="66f4c-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="66f4c-157">Dieser Ansatz wird im folgenden Beispielcode veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="66f4c-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="66f4c-158">Weisen Sie die Umgebung einer Eigenschaft der Klasse `Startup` zu.</span><span class="sxs-lookup"><span data-stu-id="66f4c-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="66f4c-159">Überprüfen Sie die Umgebung mithilfe der Eigenschaft (z. B. `if (Environment.IsDevelopment())`).</span><span class="sxs-lookup"><span data-stu-id="66f4c-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($":::no-loc(Identity):::{rule}");
            sb.Append($"User: {context.User.:::no-loc(Identity):::.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="66f4c-160">Debuggen von ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="66f4c-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="66f4c-161">Die folgenden Links bieten Informationen zum Debuggen von ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="66f4c-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="66f4c-162">Debuggen von ASP Core unter Linux</span><span class="sxs-lookup"><span data-stu-id="66f4c-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="66f4c-163">Debuggen von .NET Core unter UNIX über SSH</span><span class="sxs-lookup"><span data-stu-id="66f4c-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="66f4c-164">Schnellstart: Debuggen von ASP.NET mithilfe des Visual Studio-Debuggers</span><span class="sxs-lookup"><span data-stu-id="66f4c-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="66f4c-165">Weitere Informationen zum Debuggen finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/2960).</span><span class="sxs-lookup"><span data-stu-id="66f4c-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
