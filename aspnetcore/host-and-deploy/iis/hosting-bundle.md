---
title: Hostingpaket
author: rick-anderson
description: Hier erfahren Sie, wie Sie das .NET Core-Hostingbundle konfigurieren.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: 888f517d86cb9456ea8b933d3de842a0a21423b5
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755160"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="32253-103">Das .NET Core-Hostingbundle</span><span class="sxs-lookup"><span data-stu-id="32253-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="32253-104">Das .NET Core-Hostingbundle ist ein Installer für die .NET Core Runtime und das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="32253-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="32253-105">Mit dem Bundle können ASP.NET Core-Apps mit IIS ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="32253-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="32253-106">Installieren des .NET Core Hosting-Pakets</span><span class="sxs-lookup"><span data-stu-id="32253-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="32253-107">Wenn das Hosting-Paket vor IIS installiert wird, muss die Paketinstallation repariert werden.</span><span class="sxs-lookup"><span data-stu-id="32253-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="32253-108">Führen Sie nach der Installation von IIS erneut den Installer des Hosting-Pakets aus.</span><span class="sxs-lookup"><span data-stu-id="32253-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="32253-109">Wenn das Hosting-Paket nach der Installation der 64-Bit-Version (x64) von .NET Core installiert wird, kann es den Anschein haben, dass SDKs fehlen ([Es wurden keine .NET Core SDKs erkannt](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="32253-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="32253-110">Informationen zum Beheben des Problems finden Sie unter <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="32253-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

### <a name="direct-download-current-version"></a><span data-ttu-id="32253-111">Direkter Download (aktuelle Version)</span><span class="sxs-lookup"><span data-stu-id="32253-111">Direct download (current version)</span></span>

<span data-ttu-id="32253-112">Laden Sie den Installer über folgenden Link herunter:</span><span class="sxs-lookup"><span data-stu-id="32253-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="32253-113">Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)</span><span class="sxs-lookup"><span data-stu-id="32253-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="32253-114">Frühere Versionen des Installers</span><span class="sxs-lookup"><span data-stu-id="32253-114">Earlier versions of the installer</span></span>

<span data-ttu-id="32253-115">So erhalten Sie eine frühere Version des Installers:</span><span class="sxs-lookup"><span data-stu-id="32253-115">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="32253-116">Navigieren Sie zur [.NET Core-Downloadseite](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="32253-116">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="32253-117">Wählen Sie die gewünschte .NET Core-Version aus.</span><span class="sxs-lookup"><span data-stu-id="32253-117">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="32253-118">Suchen Sie in der Spalte **Run apps - Runtime** (Apps ausführen – Runtime) die Zeile mit der gewünschten .NET Core-RuntimeRuntime-Version.</span><span class="sxs-lookup"><span data-stu-id="32253-118">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="32253-119">Laden Sie den Installer über den Link das **Hosting Bundle** (Hosting-Paket) herunter.</span><span class="sxs-lookup"><span data-stu-id="32253-119">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="32253-120">Einige Installer enthalten Releaseversionen, die das Ende ihres Lebenszyklus erreicht haben und nicht mehr von Microsoft unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="32253-120">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="32253-121">Weitere Informationen finden Sie in den [Unterstützungsrichtlinien](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="32253-121">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="install-the-hosting-bundle"></a><span data-ttu-id="32253-122">Installieren des Hosting-Pakets</span><span class="sxs-lookup"><span data-stu-id="32253-122">Install the Hosting Bundle</span></span>

1. <span data-ttu-id="32253-123">Führen Sie das Installationsprogramm auf dem Server aus.</span><span class="sxs-lookup"><span data-stu-id="32253-123">Run the installer on the server.</span></span> <span data-ttu-id="32253-124">Die folgenden Parameter sind verfügbar, wenn Sie das Installationsprogramm über eine Administratorbefehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="32253-124">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="32253-125">`OPT_NO_ANCM=1`: Überspringen Sie die Installation des ASP.NET Core-Moduls.</span><span class="sxs-lookup"><span data-stu-id="32253-125">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="32253-126">`OPT_NO_RUNTIME=1`: Überspringen Sie die Installation der .NET Core-Runtime.</span><span class="sxs-lookup"><span data-stu-id="32253-126">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="32253-127">Wird verwendet, wenn der Server nur [eigenständige Bereitstellungen (Self-contained Deployments, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) hostet.</span><span class="sxs-lookup"><span data-stu-id="32253-127">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="32253-128">`OPT_NO_SHAREDFX=1`: Überspringen Sie die Installation des freigegebenen ASP.NET-Frameworks (ASP.NET-Runtime).</span><span class="sxs-lookup"><span data-stu-id="32253-128">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="32253-129">Wird verwendet, wenn der Server nur [eigenständige Bereitstellungen (Self-contained Deployments, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) hostet.</span><span class="sxs-lookup"><span data-stu-id="32253-129">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="32253-130">`OPT_NO_X86=1`: Überspringen Sie die Installation von x86-Runtimes.</span><span class="sxs-lookup"><span data-stu-id="32253-130">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="32253-131">Verwenden Sie diesen Parameter, wenn Sie wissen, dass Sie keine 32-Bit-Apps hosten.</span><span class="sxs-lookup"><span data-stu-id="32253-131">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="32253-132">Sollte die Möglichkeit bestehen, dass Sie sowohl 32-Bit- als auch 64-Bit-Apps hosten könnten, verwenden Sie diesen Parameter nicht, und installieren Sie beide Runtimes.</span><span class="sxs-lookup"><span data-stu-id="32253-132">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="32253-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Deaktivieren Sie die Überprüfung auf Verwendung einer gemeinsamen IIS-Konfiguration, wenn sich die gemeinsam genutzte Konfiguration (`applicationHost.config`) auf demselben Computer wie die IIS-Installation befindet.</span><span class="sxs-lookup"><span data-stu-id="32253-133">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="32253-134">*Nur für Installationsprogramme für Hostingbundles für ASP.NET Core 2.2 oder höher verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="32253-134">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="32253-135">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="32253-135">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>
1. <span data-ttu-id="32253-136">Starten Sie das System neu, oder führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="32253-136">Restart the system or execute the following commands in a command shell:</span></span>

   ```console
   net stop was /y
   net start w3svc
   ```
   <span data-ttu-id="32253-137">Durch den Neustart von IIS wird eine Änderung an der PATH-Systemeinstellung – einer Umgebungsvariable – angewendet, die durch den Installer vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="32253-137">Restarting IIS picks up a change to the system PATH, which is an environment variable, made by the installer.</span></span>

<span data-ttu-id="32253-138">ASP.NET Core wendet kein Rollforwardverhalten für Patchversionen freigegebener Frameworkpakete an.</span><span class="sxs-lookup"><span data-stu-id="32253-138">ASP.NET Core doesn't adopt roll-forward behavior for patch releases of shared framework packages.</span></span> <span data-ttu-id="32253-139">Nach einem Upgrade des freigegebene Frameworks durch Installation eines neuen Hostingpakets starten Sie das System neu, oder führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="32253-139">After upgrading the shared framework by installing a new hosting bundle, restart the system or execute the following commands in a command shell:</span></span>

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> <span data-ttu-id="32253-140">Informationen zur Verwendung einer IIS-Freigabekonfiguration finden Sie unter [ASP.NET Core-Modul mit IIS-Freigabekonfiguration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="32253-140">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="32253-141">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="32253-141">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="32253-142">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="32253-142">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="32253-143">Navigieren Sie auf dem Hostsystem zu `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span><span class="sxs-lookup"><span data-stu-id="32253-143">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="32253-144">Suchen Sie die Datei `aspnetcorev2.dll`.</span><span class="sxs-lookup"><span data-stu-id="32253-144">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="32253-145">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="32253-145">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="32253-146">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="32253-146">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="32253-147">Die Installationsprotokolle des Hostingbundles für das Modul finden Sie unter `C:\Users\%UserName%\AppData\Local\Temp`.</span><span class="sxs-lookup"><span data-stu-id="32253-147">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="32253-148">Die Datei hat den Namen `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, wobei der Platzhalter `{TIMESTAMP}` der Zeitstempel der Datei ist.</span><span class="sxs-lookup"><span data-stu-id="32253-148">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
