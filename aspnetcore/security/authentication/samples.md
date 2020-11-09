---
title: Authentifizierungs Beispiele für ASP.net Core
author: rick-anderson
description: Enthält Links zu den Authentifizierungs Beispielen im ASP.net Core Repository.
ms.author: riande
ms.date: 01/31/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/samples
ms.openlocfilehash: 4153a443748dbff40be19e25fc1c719ee4e39609
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060338"
---
# <a name="authentication-samples-for-aspnet-core"></a><span data-ttu-id="d5b46-103">Authentifizierungs Beispiele für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="d5b46-103">Authentication samples for ASP.NET Core</span></span>

<span data-ttu-id="d5b46-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d5b46-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d5b46-105">Das [ASP.net Core-Repository](https://github.com/dotnet/AspNetCore) enthält die folgenden Authentifizierungs Beispiele im Ordner " *aspnetcore/src/Security/Samples* ":</span><span class="sxs-lookup"><span data-stu-id="d5b46-105">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="d5b46-106">Transformation von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="d5b46-106">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="d5b46-107">[Cookie Genehmigung](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="d5b46-107">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="d5b46-108">Benutzerdefinierter Richtlinien Anbieter-iauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="d5b46-108">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="d5b46-109">Dynamische Authentifizierungs Schemas und-Optionen</span><span class="sxs-lookup"><span data-stu-id="d5b46-109">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="d5b46-110">[Externe Ansprüche](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="d5b46-110">[External claims](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="d5b46-111">Auswählen zwischen cookie und einem anderen Authentifizierungsschema basierend auf der Anforderung</span><span class="sxs-lookup"><span data-stu-id="d5b46-111">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="d5b46-112">Schränkt den Zugriff auf statische Dateien ein.</span><span class="sxs-lookup"><span data-stu-id="d5b46-112">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="d5b46-113">Ausführen der Beispiele</span><span class="sxs-lookup"><span data-stu-id="d5b46-113">Run the samples</span></span>

* <span data-ttu-id="d5b46-114">Wählen Sie eine [Verzweigung](https://github.com/dotnet/AspNetCore)aus.</span><span class="sxs-lookup"><span data-stu-id="d5b46-114">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="d5b46-115">Zum Beispiel, `release/3.1`</span><span class="sxs-lookup"><span data-stu-id="d5b46-115">For example, `release/3.1`</span></span>
* <span data-ttu-id="d5b46-116">Klonen Sie das [ASP.net Core Repository](https://github.com/dotnet/AspNetCore), oder laden Sie es herunter.</span><span class="sxs-lookup"><span data-stu-id="d5b46-116">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="d5b46-117">Vergewissern Sie sich, dass Sie die [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) Version installiert haben, die mit dem Klon des ASP.net Core Repository übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="d5b46-117">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="d5b46-118">Navigieren Sie in *aspnetcore/src/Security/Samples* zu einem Beispiel, und führen Sie das Beispiel mit aus `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d5b46-118">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d5b46-119">Das [ASP.net Core-Repository](https://github.com/dotnet/AspNetCore) enthält die folgenden Authentifizierungs Beispiele im Ordner " *aspnetcore/src/Security/Samples* ":</span><span class="sxs-lookup"><span data-stu-id="d5b46-119">The [ASP.NET Core repository](https://github.com/dotnet/AspNetCore) contains the following authentication samples in the *AspNetCore/src/Security/samples* folder:</span></span>

* [<span data-ttu-id="d5b46-120">Transformation von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="d5b46-120">Claims transformation</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/ClaimsTransformation)
* <span data-ttu-id="d5b46-121">[Cookie Genehmigung](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span><span class="sxs-lookup"><span data-stu-id="d5b46-121">[Cookie authentication](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Cookies)</span></span>
* [<span data-ttu-id="d5b46-122">Benutzerdefinierter Richtlinien Anbieter-iauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="d5b46-122">Custom policy provider - IAuthorizationPolicyProvider</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/CustomPolicyProvider)
* [<span data-ttu-id="d5b46-123">Dynamische Authentifizierungs Schemas und-Optionen</span><span class="sxs-lookup"><span data-stu-id="d5b46-123">Dynamic authentication schemes and options</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/DynamicSchemes)
* <span data-ttu-id="d5b46-124">[Externe Ansprüche](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span><span class="sxs-lookup"><span data-stu-id="d5b46-124">[External claims](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/Identity.ExternalClaims)</span></span>
* [<span data-ttu-id="d5b46-125">Auswählen zwischen cookie und einem anderen Authentifizierungsschema basierend auf der Anforderung</span><span class="sxs-lookup"><span data-stu-id="d5b46-125">Selecting between cookie and another authentication scheme based on the request</span></span>](https://github.com/dotnet/AspNetCore/tree/release/2.1/src/Security/samples/PathSchemeSelection)
* [<span data-ttu-id="d5b46-126">Schränkt den Zugriff auf statische Dateien ein.</span><span class="sxs-lookup"><span data-stu-id="d5b46-126">Restricts access to static files</span></span>](https://github.com/dotnet/AspNetCore/tree/2.1.3/src/Security/samples/StaticFilesAuth)

## <a name="run-the-samples"></a><span data-ttu-id="d5b46-127">Ausführen der Beispiele</span><span class="sxs-lookup"><span data-stu-id="d5b46-127">Run the samples</span></span>

* <span data-ttu-id="d5b46-128">Wählen Sie eine [Verzweigung](https://github.com/dotnet/AspNetCore)aus.</span><span class="sxs-lookup"><span data-stu-id="d5b46-128">Select a [branch](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="d5b46-129">Zum Beispiel, `release/2.1`</span><span class="sxs-lookup"><span data-stu-id="d5b46-129">For example, `release/2.1`</span></span>
* <span data-ttu-id="d5b46-130">Klonen Sie das [ASP.net Core Repository](https://github.com/dotnet/AspNetCore), oder laden Sie es herunter.</span><span class="sxs-lookup"><span data-stu-id="d5b46-130">Clone or download the [ASP.NET Core repository](https://github.com/dotnet/AspNetCore).</span></span>
* <span data-ttu-id="d5b46-131">Vergewissern Sie sich, dass Sie die [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) Version installiert haben, die mit dem Klon des ASP.net Core Repository übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="d5b46-131">Verify you have installed the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) version matching the clone of the ASP.NET Core repository.</span></span>
* <span data-ttu-id="d5b46-132">Navigieren Sie in *aspnetcore/src/Security/Samples* zu einem Beispiel, und führen Sie das Beispiel mit aus `dotnet run` .</span><span class="sxs-lookup"><span data-stu-id="d5b46-132">Navigate to a sample in *AspNetCore/src/Security/samples* and run the sample with `dotnet run`.</span></span>

::: moniker-end
