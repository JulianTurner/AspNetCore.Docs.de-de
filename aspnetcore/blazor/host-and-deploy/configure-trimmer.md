---
title: Konfigurieren des Trimmers für ASP.NET Core Blazor
author: guardrex
description: Hier erfahren Sie, wie Sie den IL-Linker (Intermediate Language, Zwischensprache) (Trimmer) beim Erstellen einer Blazor-App steuern.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2021
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
uid: blazor/host-and-deploy/configure-trimmer
ms.openlocfilehash: 41887638f13a08d375075e8377da19d1d0098c4b
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975213"
---
# <a name="configure-the-trimmer-for-aspnet-core-blazor"></a><span data-ttu-id="cd013-103">Konfigurieren des Trimmers für ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="cd013-103">Configure the Trimmer for ASP.NET Core Blazor</span></span>

<span data-ttu-id="cd013-104">Blazor WebAssembly führt eine [IL-Kürzung (Intermediate Language, Zwischensprache)](/dotnet/standard/managed-code#intermediate-language--execution) aus, um die Größe der veröffentlichten Ausgabe zu verringern.</span><span class="sxs-lookup"><span data-stu-id="cd013-104">Blazor WebAssembly performs [Intermediate Language (IL)](/dotnet/standard/managed-code#intermediate-language--execution) trimming to reduce the size of the published output.</span></span> <span data-ttu-id="cd013-105">Standardmäßig erfolgt die Kürzung beim Veröffentlichen einer App.</span><span class="sxs-lookup"><span data-stu-id="cd013-105">By default, trimming occurs when publishing an app.</span></span>

<span data-ttu-id="cd013-106">Das Kürzen kann negative Auswirkungen haben.</span><span class="sxs-lookup"><span data-stu-id="cd013-106">Trimming may have detrimental effects.</span></span> <span data-ttu-id="cd013-107">In Apps, die Reflexion verwenden, kann der Trimmer oft nicht die erforderlichen Typen für die Reflexion zur Laufzeit bestimmen.</span><span class="sxs-lookup"><span data-stu-id="cd013-107">In apps that use reflection, the Trimmer often can't determine the required types for reflection at runtime.</span></span> <span data-ttu-id="cd013-108">Zur Kürzung von Apps, die die Reflektion verwenden, muss der Trimmer über die Typen informiert werden, die für die Reflektion im Code und in Paketen oder Frameworks der App erforderlich sind, von denen die App abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="cd013-108">To trim apps that use reflection, the Trimmer must be informed about required types for reflection in both the app's code and in the packages or frameworks that the app depends on.</span></span> <span data-ttu-id="cd013-109">Der Trimmer kann auch nicht auf das dynamische Verhalten einer App zur Laufzeit reagieren.</span><span class="sxs-lookup"><span data-stu-id="cd013-109">The Trimmer is also unable to react to an app's dynamic behavior at runtime.</span></span> <span data-ttu-id="cd013-110">Wenn Sie sicherstellen möchten, dass die gekürzte App nach der Bereitstellung ordnungsgemäß funktioniert, testen Sie während der Entwicklung regelmäßig die veröffentlichte Ausgabe.</span><span class="sxs-lookup"><span data-stu-id="cd013-110">To ensure the trimmed app works correctly once deployed, test published output frequently while developing.</span></span>

<span data-ttu-id="cd013-111">Informationen zum Konfigurieren des Trimmers finden Sie im Artikel [Kürzungsoptionen](/dotnet/core/deploying/trimming-options) in der Dokumentation zu .NET-Grundlagen, die zudem Leitlinien zu folgenden Themen enthält:</span><span class="sxs-lookup"><span data-stu-id="cd013-111">To configure the Trimmer, see the [Trimming options](/dotnet/core/deploying/trimming-options) article in the .NET Fundamentals documentation, which includes guidance on the following subjects:</span></span>

* <span data-ttu-id="cd013-112">Deaktivieren der Kürzung für die gesamte App mit der Eigenschaft `<PublishTrimmed>` in der Projektdatei</span><span class="sxs-lookup"><span data-stu-id="cd013-112">Disable trimming for the entire app with the `<PublishTrimmed>` property in the project file.</span></span>
* <span data-ttu-id="cd013-113">Steuern, wie aggressiv nicht verwendete IL vom Trimmer verworfen wird</span><span class="sxs-lookup"><span data-stu-id="cd013-113">Control how aggressively unused IL is discarded by the Trimmer.</span></span>
* <span data-ttu-id="cd013-114">Verhindern, dass der Trimmer bestimmte Assemblys kürzt</span><span class="sxs-lookup"><span data-stu-id="cd013-114">Stop the Trimmer from trimming specific assemblies.</span></span>
* <span data-ttu-id="cd013-115">„Root“-Assemblys (Stammassemblys) für die Kürzung</span><span class="sxs-lookup"><span data-stu-id="cd013-115">"Root" assemblies for trimming.</span></span>
* <span data-ttu-id="cd013-116">Oberflächenwarnungen für reflektierte Typen, indem die `<SuppressTrimAnalysisWarnings>`-Eigenschaft in der Projektdatei auf `false` festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="cd013-116">Surface warnings for reflected types by setting the `<SuppressTrimAnalysisWarnings>` property to `false` in the project file.</span></span>
* <span data-ttu-id="cd013-117">Steuern der Symbolkürzung und Debuggerunterstützung</span><span class="sxs-lookup"><span data-stu-id="cd013-117">Control symbol trimming and degugger support.</span></span>
* <span data-ttu-id="cd013-118">Festlegen von Trimmerfeatures für Bibliotheksfeatures des Trimmingframeworks</span><span class="sxs-lookup"><span data-stu-id="cd013-118">Set Trimmer features for trimming framework library features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cd013-119">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cd013-119">Additional resources</span></span>

* [<span data-ttu-id="cd013-120">Kürzen eigenständiger Bereitstellungen und ausführbarer Dateien</span><span class="sxs-lookup"><span data-stu-id="cd013-120">Trim self-contained deployments and executables</span></span>](/dotnet/core/deploying/trim-self-contained)
* <xref:blazor/webassembly-performance-best-practices#intermediate-language-il-trimming>
