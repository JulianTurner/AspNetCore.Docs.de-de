---
title: 'Tools und Downloads: DevOps mit ASP.NET Core und Azure'
author: CamSoper
description: Für DevOps mit ASP.NET Core und Azure erforderliche Tools und Downloads.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 8c7f10a6b6f8504efaba6054533aba034982820c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93056568"
---
# <a name="tools-and-downloads"></a>Tools und Downloads

Azure verfügt über mehrere Schnittstellen für die Bereitstellung und Verwaltung von Ressourcen wie das [Azure-Portal](https://portal.azure.com), die [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash) und Visual Studio. In diesem Leitfaden wird ein minimalistischer Ansatz verwendet, bei dem möglichst häufig Azure Cloud Shell zum Einsatz kommt, um die Anzahl der erforderlichen Schritte zu verringern. Für manche Schritte muss jedoch das Azure-Portal verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

Folgende Abonnements sind erforderlich:

* Azure &mdash; Wenn Sie über kein Konto verfügen, [laden Sie eine kostenlose Testversion herunter](https://azure.microsoft.com/free/dotnet/).
* Azure DevOps Services &mdash; Ihr Azure DevOps-Abonnement und Ihre Organisation werden in Kapitel 4 erstellt.
* GitHub &mdash; Falls Sie noch kein Konto besitzen, können Sie sich [kostenlos dafür registrieren](https://github.com/join).

Die folgenden Tools werden benötigt:

* [Git](https://git-scm.com/downloads) &mdash; Grundlegende Kenntnisse von Git werden für diesen Leitfaden empfohlen. Lesen Sie die [Git-Dokumentation](https://git-scm.com/doc), insbesondere [git remote](https://git-scm.com/docs/git-remote) und [git push](https://git-scm.com/docs/git-push).
* [.Net Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 oder höher ist erforderlich, um die Beispiel-App zu erstellen und auszuführen. Wenn Visual Studio mit der **Plattformübergreifenden .NET Core-Entwicklung** installiert wird, ist das .NET Core SDK bereits installiert.

    Überprüfen Sie Ihre .NET Core SDK-Installation. Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Empfohlene Tools (nur Windows)

* Die stabilen Azure-Tools von [Visual Studio](https://visualstudio.microsoft.com) bieten eine GUI für die meisten der in diesem Handbuch beschriebenen Funktionen. Sie können jede beliebige Edition von Visual Studio verwenden, einschließlich der kostenlosen Visual Studio Community Edition. Die Tutorials dienen der Veranschaulichung von Entwicklung, Bereitstellung und DevOps sowohl mit als auch ohne Visual Studio.

  Vergewissern Sie sich, dass für Visual Studio die folgenden [Workloads](/visualstudio/install/modify-visual-studio) installiert sind:

  * ASP.NET und Webentwicklung
  * Azure-Entwicklung
  * Plattformübergreifende .NET Core-Entwicklung
