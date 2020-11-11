---
title: Hostingpaket
author: rick-anderson
description: Hier erfahren Sie, wie Sie das .NET Core-Hostingbundle konfigurieren.
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: a580c70d3141177be2508a0513f612eee56dbbf9
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343636"
---
# <a name="the-net-core-hosting-bundle"></a>Das .NET Core-Hostingbundle

Das .NET Core-Hostingbundle ist ein Installer für die .NET Core Runtime und das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module). Mit dem Bundle können ASP.NET Core-Apps mit IIS ausgeführt werden.

## <a name="install-the-net-core-hosting-bundle"></a>Installieren des .NET Core Hosting-Pakets

> [!IMPORTANT]
> Wenn das Hosting-Paket vor IIS installiert wird, muss die Paketinstallation repariert werden. Führen Sie nach der Installation von IIS erneut den Installer des Hosting-Pakets aus.
>
> Wenn das Hosting-Paket nach der Installation der 64-Bit-Version (x64) von .NET Core installiert wird, kann es den Anschein haben, dass SDKs fehlen ([Es wurden keine .NET Core SDKs erkannt](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Informationen zum Beheben des Problems finden Sie unter <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

## <a name="direct-download-current-version"></a>Direkter Download (aktuelle Version)

Laden Sie den Installer über folgenden Link herunter:

[Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

## <a name="visual-c-redistributable-requirement"></a>Visual C++ Redistributable-Anforderung

Installieren Sie für ältere Windows-Versionen, z. B. für Windows Server 2012 R2, Visual Studio C++ 2015, 2017, 2019 Redistributable. Andernfalls tritt im Ereignisprotokoll von Windows die folgende verwirrende Fehlermeldung auf: `The data is the error.`.

[Aktuelle x64-Version von Visual Studio C++ Redistributable](https://aka.ms/vs/16/release/vc_redist.x64.exe)
[Aktuelle x86-Version von Visual Studio C++ Redistributable](https://aka.ms/vs/16/release/vc_redist.x86.exe)

## <a name="earlier-versions-of-the-installer"></a>Frühere Versionen des Installers

So erhalten Sie eine frühere Version des Installers:

1. Navigieren Sie zur [.NET Core-Downloadseite](https://dotnet.microsoft.com/download/dotnet-core).
1. Wählen Sie die gewünschte .NET Core-Version aus.
1. Suchen Sie in der Spalte **Run apps - Runtime** (Apps ausführen – Runtime) die Zeile mit der gewünschten .NET Core-RuntimeRuntime-Version.
1. Laden Sie den Installer über den Link das **Hosting Bundle** (Hosting-Paket) herunter.

> [!WARNING]
> Einige Installer enthalten Releaseversionen, die das Ende ihres Lebenszyklus erreicht haben und nicht mehr von Microsoft unterstützt werden. Weitere Informationen finden Sie in den [Unterstützungsrichtlinien](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

## <a name="options"></a>Tastatur

1. Die folgenden Parameter sind verfügbar, wenn Sie das Installationsprogramm über eine Administratorbefehlsshell ausführen.

   * `OPT_NO_ANCM=1`: Überspringen Sie die Installation des ASP.NET Core-Moduls.
   * `OPT_NO_RUNTIME=1`: Überspringen Sie die Installation der .NET Core-Runtime. Wird verwendet, wenn der Server nur [eigenständige Bereitstellungen (Self-contained Deployments, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) hostet.
   * `OPT_NO_SHAREDFX=1`: Überspringen Sie die Installation des freigegebenen ASP.NET-Frameworks (ASP.NET-Runtime). Wird verwendet, wenn der Server nur [eigenständige Bereitstellungen (Self-contained Deployments, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) hostet.
   * `OPT_NO_X86=1`: Überspringen Sie die Installation von x86-Runtimes. Verwenden Sie diesen Parameter, wenn Sie wissen, dass Sie keine 32-Bit-Apps hosten. Sollte die Möglichkeit bestehen, dass Sie sowohl 32-Bit- als auch 64-Bit-Apps hosten könnten, verwenden Sie diesen Parameter nicht, und installieren Sie beide Runtimes.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Deaktivieren Sie die Überprüfung auf Verwendung einer gemeinsamen IIS-Konfiguration, wenn sich die gemeinsam genutzte Konfiguration (`applicationHost.config`) auf demselben Computer wie die IIS-Installation befindet. *Nur für Installationsprogramme für Hostingbundles für ASP.NET Core 2.2 oder höher verfügbar.* Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.

> [!NOTE]
> Informationen zur Verwendung einer IIS-Freigabekonfiguration finden Sie unter [ASP.NET Core-Modul mit IIS-Freigabekonfiguration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="restart-iis"></a>IIS neu starten

Nach Installation des Hostingpakets ist möglicherweise ein manueller IIS-Neustart erforderlich. Das `dotnet`-CLI-Tool (Befehl) ist unter dem Pfad zur Ausführung der IIS-Workerprozesse möglicherweise nicht vorhanden.

Zum manuellen Stoppen und Starten der IIS führen Sie die folgenden Befehle in einer Befehlsshell mit erhöhten Rechten aus:

```console
net stop was /y
net start w3svc
```

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Version des Moduls und Installerprotokolle des Hostingpakets

So ermitteln Sie die Version des installierten ASP.NET Core-Moduls:

1. Navigieren Sie auf dem Hostsystem zu `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2`.
1. Suchen Sie die Datei `aspnetcorev2.dll`.
1. Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie im Dropdownmenü die Option **Eigenschaften** aus.
1. Wählen Sie die Registerkarte **Details** aus. Die **Dateiversion** und die **Produktversion** stellen die installierte Version des Moduls dar.

Die Installationsprotokolle des Hostingbundles für das Modul finden Sie unter `C:\Users\%UserName%\AppData\Local\Temp`. Die Datei hat den Namen `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log`, wobei der Platzhalter `{TIMESTAMP}` der Zeitstempel der Datei ist.
