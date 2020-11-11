---
title: Hostingpaket
author: rick-anderson
description: Hier erfahren Sie, wie Sie das .NET Core-Hostingbundle konfigurieren.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343636"
---
# <a name="the-net-core-hosting-bundle"></a><span data-ttu-id="7ec5d-103">Das .NET Core-Hostingbundle</span><span class="sxs-lookup"><span data-stu-id="7ec5d-103">The .NET Core Hosting Bundle</span></span>

<span data-ttu-id="7ec5d-104">Das .NET Core-Hostingbundle ist ein Installer für die .NET Core Runtime und das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).</span><span class="sxs-lookup"><span data-stu-id="7ec5d-104">The .NET Core Hosting bundle is an installer for the .NET Core Runtime and the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module).</span></span> <span data-ttu-id="7ec5d-105">Mit dem Bundle können ASP.NET Core-Apps mit IIS ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-105">The bundle allows ASP.NET Core apps to run with IIS.</span></span>

## <a name="install-the-net-core-hosting-bundle"></a><span data-ttu-id="7ec5d-106">Installieren des .NET Core Hosting-Pakets</span><span class="sxs-lookup"><span data-stu-id="7ec5d-106">Install the .NET Core Hosting Bundle</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7ec5d-107">Wenn das Hosting-Paket vor IIS installiert wird, muss die Paketinstallation repariert werden.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-107">If the Hosting Bundle is installed before IIS, the bundle installation must be repaired.</span></span> <span data-ttu-id="7ec5d-108">Führen Sie nach der Installation von IIS erneut den Installer des Hosting-Pakets aus.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-108">Run the Hosting Bundle installer again after installing IIS.</span></span>
>
> <span data-ttu-id="7ec5d-109">Wenn das Hosting-Paket nach der Installation der 64-Bit-Version (x64) von .NET Core installiert wird, kann es den Anschein haben, dass SDKs fehlen ([Es wurden keine .NET Core SDKs erkannt](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span><span class="sxs-lookup"><span data-stu-id="7ec5d-109">If the Hosting Bundle is installed after installing the 64-bit (x64) version of .NET Core, SDKs might appear to be missing ([No .NET Core SDKs were detected](xref:test/troubleshoot#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="7ec5d-110">Informationen zum Beheben des Problems finden Sie unter <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-110">To resolve the problem, see <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.</span></span>

## <a name="direct-download-current-version"></a><span data-ttu-id="7ec5d-111">Direkter Download (aktuelle Version)</span><span class="sxs-lookup"><span data-stu-id="7ec5d-111">Direct download (current version)</span></span>

<span data-ttu-id="7ec5d-112">Laden Sie den Installer über folgenden Link herunter:</span><span class="sxs-lookup"><span data-stu-id="7ec5d-112">Download the installer using the following link:</span></span>

[<span data-ttu-id="7ec5d-113">Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)</span><span class="sxs-lookup"><span data-stu-id="7ec5d-113">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a><span data-ttu-id="7ec5d-114">Visual C++ Redistributable-Anforderung</span><span class="sxs-lookup"><span data-stu-id="7ec5d-114">Visual C++ Redistributable Requirement</span></span>

<span data-ttu-id="7ec5d-115">Installieren Sie für ältere Windows-Versionen, z. B. für Windows Server 2012 R2, Visual Studio C++ 2015, 2017, 2019 Redistributable.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-115">On older versions of Windows, for example Windows Server 2012 R2, install the Visual Studio C++ 2015, 2017, 2019 Redistributable.</span></span> <span data-ttu-id="7ec5d-116">Andernfalls tritt im Ereignisprotokoll von Windows die folgende verwirrende Fehlermeldung auf: `The data is the error.`.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-116">Otherwise, a confusing error message in the Windows Event Log reports that `The data is the error.`</span></span>

<span data-ttu-id="7ec5d-117">[Aktuelle x64-Version von Visual Studio C++ Redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Aktuelle x86-Version von Visual Studio C++ Redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span><span class="sxs-lookup"><span data-stu-id="7ec5d-117">[Current x64 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Current x86 VS C++ redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)</span></span>

## <a name="earlier-versions-of-the-installer"></a><span data-ttu-id="7ec5d-118">Frühere Versionen des Installers</span><span class="sxs-lookup"><span data-stu-id="7ec5d-118">Earlier versions of the installer</span></span>

<span data-ttu-id="7ec5d-119">So erhalten Sie eine frühere Version des Installers:</span><span class="sxs-lookup"><span data-stu-id="7ec5d-119">To obtain an earlier version of the installer:</span></span>

1. <span data-ttu-id="7ec5d-120">Navigieren Sie zur [.NET Core-Downloadseite](https://dotnet.microsoft.com/download/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="7ec5d-120">Navigate to the [Download .NET Core](https://dotnet.microsoft.com/download/dotnet-core) page.</span></span>
1. <span data-ttu-id="7ec5d-121">Wählen Sie die gewünschte .NET Core-Version aus.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-121">Select the desired .NET Core version.</span></span>
1. <span data-ttu-id="7ec5d-122">Suchen Sie in der Spalte **Run apps - Runtime** (Apps ausführen – Runtime) die Zeile mit der gewünschten .NET Core-RuntimeRuntime-Version.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-122">In the **Run apps - Runtime** column, find the row of the .NET Core runtime version desired.</span></span>
1. <span data-ttu-id="7ec5d-123">Laden Sie den Installer über den Link das **Hosting Bundle** (Hosting-Paket) herunter.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-123">Download the installer using the **Hosting Bundle** link.</span></span>

> [!WARNING]
> <span data-ttu-id="7ec5d-124">Einige Installer enthalten Releaseversionen, die das Ende ihres Lebenszyklus erreicht haben und nicht mehr von Microsoft unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-124">Some installers contain release versions that have reached their end of life (EOL) and are no longer supported by Microsoft.</span></span> <span data-ttu-id="7ec5d-125">Weitere Informationen finden Sie in den [Unterstützungsrichtlinien](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="7ec5d-125">For more information, see the [support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

## <a name="options"></a><span data-ttu-id="7ec5d-126">Tastatur</span><span class="sxs-lookup"><span data-stu-id="7ec5d-126">Options</span></span>

1. <span data-ttu-id="7ec5d-127">Die folgenden Parameter sind verfügbar, wenn Sie das Installationsprogramm über eine Administratorbefehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-127">The following parameters are available when running the installer from an administrator command shell:</span></span>

   * <span data-ttu-id="7ec5d-128">`OPT_NO_ANCM=1`: Überspringen Sie die Installation des ASP.NET Core-Moduls.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-128">`OPT_NO_ANCM=1`: Skip installing the ASP.NET Core Module.</span></span>
   * <span data-ttu-id="7ec5d-129">`OPT_NO_RUNTIME=1`: Überspringen Sie die Installation der .NET Core-Runtime.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-129">`OPT_NO_RUNTIME=1`: Skip installing the .NET Core runtime.</span></span> <span data-ttu-id="7ec5d-130">Wird verwendet, wenn der Server nur [eigenständige Bereitstellungen (Self-contained Deployments, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) hostet.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-130">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7ec5d-131">`OPT_NO_SHAREDFX=1`: Überspringen Sie die Installation des freigegebenen ASP.NET-Frameworks (ASP.NET-Runtime).</span><span class="sxs-lookup"><span data-stu-id="7ec5d-131">`OPT_NO_SHAREDFX=1`: Skip installing the ASP.NET Shared Framework (ASP.NET runtime).</span></span> <span data-ttu-id="7ec5d-132">Wird verwendet, wenn der Server nur [eigenständige Bereitstellungen (Self-contained Deployments, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) hostet.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-132">Used when the server only hosts [self-contained deployments (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>
   * <span data-ttu-id="7ec5d-133">`OPT_NO_X86=1`: Überspringen Sie die Installation von x86-Runtimes.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-133">`OPT_NO_X86=1`: Skip installing x86 runtimes.</span></span> <span data-ttu-id="7ec5d-134">Verwenden Sie diesen Parameter, wenn Sie wissen, dass Sie keine 32-Bit-Apps hosten.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-134">Use this parameter when you know that you won't be hosting 32-bit apps.</span></span> <span data-ttu-id="7ec5d-135">Sollte die Möglichkeit bestehen, dass Sie sowohl 32-Bit- als auch 64-Bit-Apps hosten könnten, verwenden Sie diesen Parameter nicht, und installieren Sie beide Runtimes.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-135">If there's any chance that you will host both 32-bit and 64-bit apps in the future, don't use this parameter and install both runtimes.</span></span>
   * <span data-ttu-id="7ec5d-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Deaktivieren Sie die Überprüfung auf Verwendung einer gemeinsamen IIS-Konfiguration, wenn sich die gemeinsam genutzte Konfiguration (`applicationHost.config`) auf demselben Computer wie die IIS-Installation befindet.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-136">`OPT_NO_SHARED_CONFIG_CHECK=1`: Disable the check for using an IIS Shared Configuration when the shared configuration (`applicationHost.config`) is on the same machine as the IIS installation.</span></span> <span data-ttu-id="7ec5d-137">*Nur für Installationsprogramme für Hostingbundles für ASP.NET Core 2.2 oder höher verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="7ec5d-137">*Only available for ASP.NET Core 2.2 or later Hosting Bundler installers.*</span></span> <span data-ttu-id="7ec5d-138">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-138">For more information, see <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.</span></span>

> [!NOTE]
> <span data-ttu-id="7ec5d-139">Informationen zur Verwendung einer IIS-Freigabekonfiguration finden Sie unter [ASP.NET Core-Modul mit IIS-Freigabekonfiguration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span><span class="sxs-lookup"><span data-stu-id="7ec5d-139">For information on IIS Shared Configuration, see [ASP.NET Core Module with IIS Shared Configuration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).</span></span>

## <a name="restart-iis"></a><span data-ttu-id="7ec5d-140">IIS neu starten</span><span class="sxs-lookup"><span data-stu-id="7ec5d-140">Restart IIS</span></span>

<span data-ttu-id="7ec5d-141">Nach Installation des Hostingpakets ist möglicherweise ein manueller IIS-Neustart erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-141">After the Hosting Bundle is installed, a manual IIS restart may be required.</span></span> <span data-ttu-id="7ec5d-142">Das `dotnet`-CLI-Tool (Befehl) ist unter dem Pfad zur Ausführung der IIS-Workerprozesse möglicherweise nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-142">For example, the `dotnet` CLI tooling (command) might not exist on the PATH for running IIS worker processes.</span></span>

<span data-ttu-id="7ec5d-143">Zum manuellen Stoppen und Starten der IIS führen Sie die folgenden Befehle in einer Befehlsshell mit erhöhten Rechten aus:</span><span class="sxs-lookup"><span data-stu-id="7ec5d-143">To manually stop and start IIS, execute the following commands in an elevated command shell:</span></span>

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a><span data-ttu-id="7ec5d-144">Version des Moduls und Installerprotokolle des Hostingpakets</span><span class="sxs-lookup"><span data-stu-id="7ec5d-144">Module version and Hosting Bundle installer logs</span></span>

<span data-ttu-id="7ec5d-145">So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="7ec5d-145">To determine the version of the installed ASP.NET Core Module:</span></span>

1. <span data-ttu-id="7ec5d-146">Navigieren Sie auf dem Hostsystem zu `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-146">On the hosting system, navigate to `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.</span></span>
1. <span data-ttu-id="7ec5d-147">Suchen Sie die Datei `aspnetcorev2.dll`.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-147">Locate the `aspnetcorev2.dll` file.</span></span>
1. <span data-ttu-id="7ec5d-148">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-148">Right-click the file and select **Properties** from the contextual menu.</span></span>
1. <span data-ttu-id="7ec5d-149">Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-149">Select the **Details** tab. The **File version** and **Product version** represent the installed version of the module.</span></span>

<span data-ttu-id="7ec5d-150">Die Installationsprotokolle des Hostingbundles für das Modul finden Sie unter `C:\Users\%UserName%\AppData\Local\Temp`.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-150">The Hosting Bundle installer logs for the module are found at `C:\Users\%UserName%\AppData\Local\Temp`.</span></span> <span data-ttu-id="7ec5d-151">Die Datei hat den Namen `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, wobei der Platzhalter `{TIMESTAMP}` der Zeitstempel der Datei ist.</span><span class="sxs-lookup"><span data-stu-id="7ec5d-151">The file is named `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, where the placeholder `{TIMESTAMP}` is the timestamp of the file.</span></span>
