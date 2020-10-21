---
title: App-Offlinedatei (app_offline.htm)
author: rick-anderson
description: Hier erhalten Sie Informationen dazu, wie die App-Offlinedatei (`app_offline.htm`) mit dem ASP.NET Core-Modul verwendet werden kann.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 95dfadd084af5909fee754308ad5d65f54d4875d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755164"
---
# <a name="app-offline-file-app_offlinehtm"></a><span data-ttu-id="69909-103">App-Offlinedatei (`app_offline.htm`)</span><span class="sxs-lookup"><span data-stu-id="69909-103">App Offline file (`app_offline.htm`)</span></span>

<span data-ttu-id="69909-104">Die App-Offlinedatei (`app_offline.htm`) wird vom ASP.NET Core-Modul verwendet, um eine App herunterzufahren.</span><span class="sxs-lookup"><span data-stu-id="69909-104">The App Offline file (`app_offline.htm`) is used by the ASP.NET Core Module to shut down an app.</span></span>

<span data-ttu-id="69909-105">Wenn eine Datei mit dem Namen `app_offline.htm` im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="69909-105">If a file with the name `app_offline.htm` is detected in the root directory of an app, the ASP.NET Core Module attempts to gracefully shut down the app and stop processing incoming requests.</span></span> <span data-ttu-id="69909-106">Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurde, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.</span><span class="sxs-lookup"><span data-stu-id="69909-106">If the app is still running after the number of seconds defined in `shutdownTimeLimit`, the ASP.NET Core Module stops the running process.</span></span>

<span data-ttu-id="69909-107">Wenn die Datei `app_offline.htm` vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der `app_offline.htm`-Datei zurücksendet.</span><span class="sxs-lookup"><span data-stu-id="69909-107">While the `app_offline.htm` file is present, the ASP.NET Core Module responds to requests by sending back the contents of the `app_offline.htm` file.</span></span> <span data-ttu-id="69909-108">Wenn die Datei `app_offline.htm` entfernt wurde, wird die App von der nächsten Anforderung gestartet.</span><span class="sxs-lookup"><span data-stu-id="69909-108">When the `app_offline.htm` file is removed, the next request starts the app.</span></span>

<span data-ttu-id="69909-109">Beim Verwenden des Out-of-Process-Hostingmodells wird die App möglicherweise nicht sofort heruntergefahren, wenn eine offene Verbindung besteht.</span><span class="sxs-lookup"><span data-stu-id="69909-109">When using the out-of-process hosting model, the app might not shut down immediately if there's an open connection.</span></span> <span data-ttu-id="69909-110">Beispielsweise kann eine WebSocket-Verbindung eine Verzögerung beim Herunterfahren der App bewirken.</span><span class="sxs-lookup"><span data-stu-id="69909-110">For example, a WebSocket connection may delay app shut down.</span></span>

## <a name="locked-deployment-files"></a><span data-ttu-id="69909-111">Gesperrte Bereitstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="69909-111">Locked deployment files</span></span>

<span data-ttu-id="69909-112">Die Dateien im Bereitstellungsordner werden gesperrt, wenn die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="69909-112">Files in the deployment folder are locked when the app is running.</span></span> <span data-ttu-id="69909-113">Gesperrte Dateien können während der Bereitstellung nicht überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="69909-113">Locked files can't be overwritten during deployment.</span></span>

<span data-ttu-id="69909-114">`app_offline.htm` ist die primäre Methode, mit der gesperrte Dateien entsperrt werden.</span><span class="sxs-lookup"><span data-stu-id="69909-114">`app_offline.htm` is the primary mechanism to release locked files.</span></span> <span data-ttu-id="69909-115">`app_offline.htm` wird von Web Deploy verwendet, um die App ordnungsgemäß anzuhalten und zu starten.</span><span class="sxs-lookup"><span data-stu-id="69909-115">`app_offline.htm` is used by Web Deploy to properly stop and start the app.</span></span>

<span data-ttu-id="69909-116">`app_offline.htm` kann manuell verwendet werden, um die App zu starten und anzuhalten (PowerShell 5 oder höher erforderlich):</span><span class="sxs-lookup"><span data-stu-id="69909-116">`app_offline.htm` can be manually used to start and stop the app (requires PowerShell 5 or later):</span></span>

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

<span data-ttu-id="69909-117">Für das vorangehende PowerShell-Skript gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="69909-117">In the preceding PowerShell script:</span></span>

* <span data-ttu-id="69909-118">Der Platzhalter `{PATH TO APP}` ist der Pfad zur App.</span><span class="sxs-lookup"><span data-stu-id="69909-118">The placeholder `{PATH TO APP}` is the path to the app.</span></span>
* <span data-ttu-id="69909-119">Der `New-Item`-Befehl hält den App-Pool an.</span><span class="sxs-lookup"><span data-stu-id="69909-119">The `New-Item` command stops the app pool.</span></span>
* <span data-ttu-id="69909-120">Der `Remove-Item`-Befehl startet den App-Pool.</span><span class="sxs-lookup"><span data-stu-id="69909-120">The `Remove-Item` command starts the app pool.</span></span>
* <span data-ttu-id="69909-121">Befehle zwischen dem `New-Item`-Befehl und dem `Remove-Item`-Befehl werden vom Entwickler bereitgestellt, um die App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="69909-121">Commands between the `New-Item` command and the `Remove-Item` command are provided by the developer to deploy the app.</span></span>

<span data-ttu-id="69909-122">Dateien können auch entsperrt werden, indem der App-Pool vom IIS-Manager auf dem Server manuell angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="69909-122">Files can also be unlocked by manually stopping the app pool in the IIS Manager on the server.</span></span> <span data-ttu-id="69909-123">Verwenden Sie die `app_offine.htm`-Datei nicht, wenn der IIS-Manager verwendet wird, um den App-Pool anzuhalten und neu zu starten.</span><span class="sxs-lookup"><span data-stu-id="69909-123">Don't use the `app_offine.htm` file when using the IIS Manager to stop and restart the app pool.</span></span>