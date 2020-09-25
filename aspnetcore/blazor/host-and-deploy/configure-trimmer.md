---
title: Konfigurieren des Trimmers für ASP.NET Core Blazor
author: guardrex
description: Hier erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) (Trimmer) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/14/2020
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 2923f76c586465e4e6044763f18527a7d36ad57c
ms.sourcegitcommit: 600666440398788db5db25dc0496b9ca8fe50915
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/14/2020
ms.locfileid: "90080849"
---
# <a name="configure-the-trimmer-for-aspnet-core-no-locblazor"></a><span data-ttu-id="4e9c9-103">Konfigurieren des Trimmers für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="4e9c9-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="4e9c9-104">Von [Pranav Krishnamoorthy](https://github.com/pranavkm)</span><span class="sxs-lookup"><span data-stu-id="4e9c9-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm)</span></span>

<span data-ttu-id="4e9c9-105">Blazor WebAssembly führt eine [IL-Kürzung (Intermediate Language, Zwischensprache)](/dotnet/standard/managed-code#intermediate-language--execution) aus, um die Größe der veröffentlichten Ausgabe zu verringern.</span><span class="sxs-lookup"><span data-stu-id="4e9c9-105">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span>

<span data-ttu-id="4e9c9-106">Durch das Kürzen einer App wird die Größe optimiert. Dies kann jedoch auch negative Auswirkungen haben.</span><span class="sxs-lookup"><span data-stu-id="4e9c9-106">Trimming an app optimizes for size but may have detrimental effects.</span></span> <span data-ttu-id="4e9c9-107">Apps, die Reflektion oder ähnliche dynamische Features verwenden, können beim Kürzen unterbrochen werden, da der Trimmer dieses dynamische Verhalten nicht kennt und nicht ermitteln kann, welche Typen für die Reflektion zur Laufzeit erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="4e9c9-107">Apps that use reflection or related dynamic features may break when trimmed because the trimmer doesn't know about dynamic behavior and can't determine in general which types are required for reflection at runtime.</span></span> <span data-ttu-id="4e9c9-108">Zur Kürzung solcher Apps muss der Trimmer über alle Typen informiert werden, die für die Reflektion im Code und in Paketen oder Frameworks erforderlich sind, von denen die App abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="4e9c9-108">To trim such apps, the trimmer must be informed about any types required by reflection in the code and in packages or frameworks that the app depends on.</span></span>

<span data-ttu-id="4e9c9-109">Wenn Sie sicherstellen möchten, dass die gekürzte App nach der Bereitstellung ordnungsgemäß funktioniert, ist es wichtig, bei der Entwicklung veröffentlichte Ausgaben häufig zu testen.</span><span class="sxs-lookup"><span data-stu-id="4e9c9-109">To ensure the trimmed app works correctly once deployed, it's important to test published output frequently while developing.</span></span>

<span data-ttu-id="4e9c9-110">Das Kürzen kann für .NET-Apps deaktiviert werden, indem die MSBuild-Eigenschaft `PublishTrimmed` in der Projektdatei der App auf `false` festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="4e9c9-110">Trimming for .NET apps can be disabled by setting the `PublishTrimmed` MSBuild property to `false` in the app's project file:</span></span>

```xml
<PropertyGroup>
  <PublishTrimmed>false</PublishTrimmed>
</PropertyGroup>
```

## <a name="additional-resources"></a><span data-ttu-id="4e9c9-111">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4e9c9-111">Additional resources</span></span>

* [<span data-ttu-id="4e9c9-112">Kürzen eigenständiger Bereitstellungen und ausführbarer Dateien</span><span class="sxs-lookup"><span data-stu-id="4e9c9-112">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
