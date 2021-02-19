---
title: Verwenden von ASP.NET Core-APIs in einer Klassenbibliothek
author: scottaddie
description: Erfahren Sie, wie ASP.NET Core-APIs in einer Klassenbibliothek verwendet werden.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: c012658a6f48247af60c8bfd56a7d987f6aa8a68
ms.sourcegitcommit: c1839f2992b003c92cd958244a2e0771ae928786
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "93061508"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="d94f5-103">Verwenden von ASP.NET Core-APIs in einer Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="d94f5-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="d94f5-104">Von [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="d94f5-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="d94f5-105">Dieses Dokument enthält Anleitungen zum Verwenden von ASP.NET Core-APIs in einer Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="d94f5-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="d94f5-106">Weitere Informationen zur Bibliothek finden Sie unter [Open-Source-Bibliotheksleitfaden](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="d94f5-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="d94f5-107">Bestimmen der zu unterstützenden ASP.NET Core-Versionen</span><span class="sxs-lookup"><span data-stu-id="d94f5-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="d94f5-108">ASP.NET Core entspricht der [.NET Core-Unterstützungsrichtlinie](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="d94f5-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="d94f5-109">Überprüfen Sie beim Bestimmen der in einer Bibliothek zu unterstützenden ASP.NET Core-Versionen die Supportrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="d94f5-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="d94f5-110">Eine Bibliothek sollte diese Merkmale aufweisen:</span><span class="sxs-lookup"><span data-stu-id="d94f5-110">A library should:</span></span>

* <span data-ttu-id="d94f5-111">Alle als *Long-Term Support* (LTS) klassifizierten ASP.NET Core-Versionen zu unterstützen versuchen.</span><span class="sxs-lookup"><span data-stu-id="d94f5-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="d94f5-112">Sich nicht der Unterstützung von ASP.NET Core-Versionen verschreiben, die als *End of Life* (EOL) klassifiziert sind.</span><span class="sxs-lookup"><span data-stu-id="d94f5-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="d94f5-113">Wenn Vorschauversionen von ASP.NET Core verfügbar gemacht werden, werden nicht abwärtskompatible Änderungen im GitHub-Repository [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="d94f5-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="d94f5-114">Kompatibilitätstests von Bibliotheken können im Rahmen der Entwicklung von Frameworkfeatures durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="d94f5-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="d94f5-115">Verwenden des freigegebenen ASP.NET Core-Frameworks</span><span class="sxs-lookup"><span data-stu-id="d94f5-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="d94f5-116">Mit der Veröffentlichung von .NET Core 3.0 werden viele ASP.NET Core-Assemblys nicht mehr als Pakete in NuGet veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="d94f5-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="d94f5-117">Stattdessen sind die Assemblys im freigegebenen Framework `Microsoft.AspNetCore.App` enthalten, das mit dem .NET Core SDK und Runtimeinstallationsprogrammen installiert wird.</span><span class="sxs-lookup"><span data-stu-id="d94f5-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="d94f5-118">Eine Liste der Pakete, die nicht mehr veröffentlicht werden, finden Sie unter [Entfernen von Verweisen auf veraltete Pakete](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="d94f5-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="d94f5-119">Seit .NET Core 3.0 verweisen Projekte, die das `Microsoft.NET.Sdk.Web` MSBuild SDK verwenden, implizit auf das freigegebene Framework.</span><span class="sxs-lookup"><span data-stu-id="d94f5-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="d94f5-120">Projekte, die das `Microsoft.NET.Sdk` oder `Microsoft.NET.Sdk.Razor` SDK verwenden, müssen auf ASP.NET Core verweisen, um ASP.NET Core-APIs im freigegebenen Framework zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d94f5-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="d94f5-121">Um auf ASP.NET Core zu verweisen, fügen Sie Ihrer Projektdatei das folgende `<FrameworkReference>`-Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="d94f5-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="d94f5-122">Der Verweis auf ASP.NET Core in dieser Weise wird nur für Projekte mit der Zielplattform .NET Core 3.x unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d94f5-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-blazor-extensibility"></a><span data-ttu-id="d94f5-123">Einschließen von Blazor-Erweiterbarkeit</span><span class="sxs-lookup"><span data-stu-id="d94f5-123">Include Blazor extensibility</span></span>

<span data-ttu-id="d94f5-124">Blazor unterstützt die [Hostingmodelle](xref:blazor/hosting-models) WebAssembly (WASM) und Server.</span><span class="sxs-lookup"><span data-stu-id="d94f5-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="d94f5-125">Wenn es keinen bestimmten Grund gibt, der dagegen spricht, sollte eine [Razor-Komponenten](xref:blazor/components/index)-Bibliothek beide Hostingmodelle unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d94f5-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components/index) library should support both hosting models.</span></span> <span data-ttu-id="d94f5-126">Eine Razor-Komponentenbibliothek muss das [Microsoft.NET.Sdk.Razor-SDK ](xref:razor-pages/sdk) verwenden.</span><span class="sxs-lookup"><span data-stu-id="d94f5-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="d94f5-127">Unterstützen beider Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="d94f5-127">Support both hosting models</span></span>

<span data-ttu-id="d94f5-128">Verwenden Sie zur Unterstützung der Nutzung von Razor-Komponenten sowohl in [Blazor Server](xref:blazor/hosting-models#blazor-server)- als auch in [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly)-Projekten die folgenden Anweisungen für Ihren Editor.</span><span class="sxs-lookup"><span data-stu-id="d94f5-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d94f5-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d94f5-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d94f5-130">Verwenden Sie die Projektvorlage **Razor-Klassenbibliothek**.</span><span class="sxs-lookup"><span data-stu-id="d94f5-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="d94f5-131">Das Kontrollkästchen **Seiten und Ansichten unterstützen** sollte deaktiviert sein.</span><span class="sxs-lookup"><span data-stu-id="d94f5-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d94f5-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d94f5-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d94f5-133">Führen Sie im integrierten Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d94f5-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d94f5-134">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d94f5-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d94f5-135">Verwenden Sie die Projektvorlage **Razor-Klassenbibliothek**.</span><span class="sxs-lookup"><span data-stu-id="d94f5-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="d94f5-136">Das aus der Vorlage generierte Projekt weist diese Eigenschaften auf:</span><span class="sxs-lookup"><span data-stu-id="d94f5-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="d94f5-137">Zielplattform .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="d94f5-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="d94f5-138">Legt die `RazorLangVersion`-Eigenschaft auf `3.0` fest.</span><span class="sxs-lookup"><span data-stu-id="d94f5-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="d94f5-139">`3.0` ist der Standardwert für .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="d94f5-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="d94f5-140">Fügt die folgenden Paketverweise hinzu:</span><span class="sxs-lookup"><span data-stu-id="d94f5-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="d94f5-141">Microsoft.AspNetCore.Components</span><span class="sxs-lookup"><span data-stu-id="d94f5-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="d94f5-142">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="d94f5-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="d94f5-143">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d94f5-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="d94f5-144">Unterstützen eines bestimmten Hostingmodells</span><span class="sxs-lookup"><span data-stu-id="d94f5-144">Support a specific hosting model</span></span>

<span data-ttu-id="d94f5-145">Es ist weitaus weniger üblich, ein einzelnes Blazor-Hostingmodell zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d94f5-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="d94f5-146">So wird beispielsweise die Nutzung von Razor-Komponenten nur in [Blazor Server](xref:blazor/hosting-models#blazor-server)-Projekten unterstützt:</span><span class="sxs-lookup"><span data-stu-id="d94f5-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="d94f5-147">Legen Sie .NET Core 3.x als Ziel fest.</span><span class="sxs-lookup"><span data-stu-id="d94f5-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="d94f5-148">Fügen Sie ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="d94f5-149">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d94f5-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="d94f5-150">Weitere Informationen zu Bibliotheken, die Razor-Komponenten enthalten, finden Sie unter [Klassenbibliotheken für ASP.NET Core-Razor-Komponenten](xref:blazor/components/class-libraries).</span><span class="sxs-lookup"><span data-stu-id="d94f5-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/components/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="d94f5-151">Einschließen von MVC-Erweiterbarkeit</span><span class="sxs-lookup"><span data-stu-id="d94f5-151">Include MVC extensibility</span></span>

<span data-ttu-id="d94f5-152">In diesem Abschnitt werden Empfehlungen für Bibliotheken erläutert, die Folgendes umfassen:</span><span class="sxs-lookup"><span data-stu-id="d94f5-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="d94f5-153">Razor-Ansichten oder Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="d94f5-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="d94f5-154">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="d94f5-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="d94f5-155">Ansichtskomponenten</span><span class="sxs-lookup"><span data-stu-id="d94f5-155">View components</span></span>](#view-components)

<span data-ttu-id="d94f5-156">In diesem Abschnitt wird nicht die Unterstützung mehrerer Zielplattformen erörtert, um mehrere Versionen von MVC zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="d94f5-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="d94f5-157">Anleitungen zum Unterstützen mehrerer ASP.NET Core-Versionen finden Sie unter [Unterstützung mehrerer ASP.NET Core-Versionen](#support-multiple-aspnet-core-versions).</span><span class="sxs-lookup"><span data-stu-id="d94f5-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="razor-views-or-razor-pages"></a><span data-ttu-id="d94f5-158">Razor-Ansichten oder Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="d94f5-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="d94f5-159">Ein Projekt, das [Razor-Ansichten](xref:mvc/views/overview) oder [Razor-Seiten](xref:razor-pages/index) enthält, muss das [Microsoft.NET.Sdk.Razor-SDK ](xref:razor-pages/sdk) verwenden.</span><span class="sxs-lookup"><span data-stu-id="d94f5-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="d94f5-160">Wenn für das Projekt .NET Core 3.x als Ziel festgelegt ist, wird Folgendes benötigt:</span><span class="sxs-lookup"><span data-stu-id="d94f5-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="d94f5-161">Eine `AddRazorSupportForMvc`-MSBuild-Eigenschaft, die auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d94f5-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="d94f5-162">Ein `<FrameworkReference>`-Element für das freigegebene Framework.</span><span class="sxs-lookup"><span data-stu-id="d94f5-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="d94f5-163">Die Projektvorlage **Razor-Klassenbibliothek** erfüllt die obigen Voraussetzungen für Projekte für .NET Core 3.x.</span><span class="sxs-lookup"><span data-stu-id="d94f5-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="d94f5-164">Verwenden Sie die folgenden Anweisungen für Ihren Editor.</span><span class="sxs-lookup"><span data-stu-id="d94f5-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d94f5-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d94f5-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d94f5-166">Verwenden Sie die Projektvorlage **Razor-Klassenbibliothek**.</span><span class="sxs-lookup"><span data-stu-id="d94f5-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="d94f5-167">Das Kontrollkästchen **Seiten und Ansichten unterstützen** sollte aktiviert sein.</span><span class="sxs-lookup"><span data-stu-id="d94f5-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d94f5-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d94f5-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d94f5-169">Führen Sie im integrierten Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d94f5-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d94f5-170">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d94f5-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d94f5-171">Zurzeit ist keine Unterstützung durch Projektvorlagen verfügbar.</span><span class="sxs-lookup"><span data-stu-id="d94f5-171">No project template support at this time.</span></span>

---

<span data-ttu-id="d94f5-172">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d94f5-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="d94f5-173">Wenn das Projekt stattdessen .NET Standard als Ziel hat, ist ein Paketverweis auf [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="d94f5-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="d94f5-174">Das `Microsoft.AspNetCore.Mvc`-Paket wurde in ASP.NET Core 3.0 in das freigegebene Framework verschoben und wird daher nicht mehr veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="d94f5-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="d94f5-175">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d94f5-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="d94f5-176">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="d94f5-176">Tag Helpers</span></span>

<span data-ttu-id="d94f5-177">Ein Projekt, das [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) umfasst, sollte das `Microsoft.NET.Sdk` SDK verwenden.</span><span class="sxs-lookup"><span data-stu-id="d94f5-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="d94f5-178">Fügen Sie für .NET Core 3.x als Ziel ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="d94f5-179">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d94f5-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="d94f5-180">Bei .NET Standard als Ziel (um Versionen vor ASP.NET Core 3.x zu unterstützen), fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="d94f5-181">Das `Microsoft.AspNetCore.Mvc.Razor`-Paket wurde in das freigegebene Framework verschoben und wird daher nicht mehr veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="d94f5-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="d94f5-182">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d94f5-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="d94f5-183">Ansichtskomponenten</span><span class="sxs-lookup"><span data-stu-id="d94f5-183">View components</span></span>

<span data-ttu-id="d94f5-184">Ein Projekt, das [Ansichtskomponenten](xref:mvc/views/view-components) umfasst, sollte das `Microsoft.NET.Sdk` SDK verwenden.</span><span class="sxs-lookup"><span data-stu-id="d94f5-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="d94f5-185">Fügen Sie für .NET Core 3.x als Ziel ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="d94f5-186">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d94f5-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="d94f5-187">Bei .NET Standard als Ziel (um Versionen vor ASP.NET Core 3.x zu unterstützen), fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="d94f5-188">Das `Microsoft.AspNetCore.Mvc.ViewFeatures`-Paket wurde in das freigegebene Framework verschoben und wird daher nicht mehr veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="d94f5-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="d94f5-189">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d94f5-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="d94f5-190">Unterstützung mehrerer ASP.NET Core-Versionen</span><span class="sxs-lookup"><span data-stu-id="d94f5-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="d94f5-191">Multi-Targeting ist erforderlich, um eine Bibliothek zu erstellen, die mehrere Varianten von ASP.NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d94f5-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="d94f5-192">Stellen Sie sich ein Szenario vor, in dem eine Taghilfsprogramm-Bibliothek die folgenden ASP.NET Core-Varianten unterstützen muss:</span><span class="sxs-lookup"><span data-stu-id="d94f5-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="d94f5-193">ASP.NET Core 2.1 mit dem Ziel .NET Framework 4.6.1</span><span class="sxs-lookup"><span data-stu-id="d94f5-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="d94f5-194">ASP.NET Core 2.x mit dem Ziel .NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="d94f5-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="d94f5-195">ASP.NET Core 3.x mit dem Ziel .NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="d94f5-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="d94f5-196">Die folgende Projektdatei unterstützt diese Varianten über die `TargetFrameworks`-Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="d94f5-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="d94f5-197">Dies erfolgt mit der vorstehenden Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="d94f5-197">With the preceding project file:</span></span>

* <span data-ttu-id="d94f5-198">Das `Markdig`-Paket wird für alle Consumer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d94f5-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="d94f5-199">Ein Verweis auf [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span><span class="sxs-lookup"><span data-stu-id="d94f5-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span></span> <span data-ttu-id="d94f5-200">wird für Consumer mit dem Ziel .NET Framework 4.6.1 oder höher oder .NET Core 2.x hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d94f5-200">is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="d94f5-201">Die Version 2.1.0 des Pakets funktioniert aufgrund der Abwärtskompatibilität mit ASP.NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="d94f5-201">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="d94f5-202">Auf das freigegebene Framework wird für Consumer mit dem Ziel .NET Core 3.x verwiesen.</span><span class="sxs-lookup"><span data-stu-id="d94f5-202">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="d94f5-203">Das `Microsoft.AspNetCore.Mvc.Razor`-Paket ist im freigegebenen Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="d94f5-203">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="d94f5-204">Alternativ könnte .NET Standard 2.0 als Ziel verwendet werden, statt sowohl .NET Core 2.1 als auch .NET Framework 4.6.1 als Ziel festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d94f5-204">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="d94f5-205">Für die vorstehende Projektdatei gelten die folgenden Vorbehalte:</span><span class="sxs-lookup"><span data-stu-id="d94f5-205">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="d94f5-206">Da die Bibliothek nur Taghilfsprogramme enthält, ist es einfacher, auf die spezifischen Plattformen zu abzuzielen, auf denen ASP.NET Core ausgeführt wird: .NET Core und .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="d94f5-206">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="d94f5-207">Taghilfsprogramme können von anderen mit .NET Standard 2.0 kompatiblen Zielframeworks wie Unity, UWP und Xamarin nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d94f5-207">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="d94f5-208">Bei der Verwendung von .NET Standard 2.0 aus .NET Framework gibt es einige Probleme, die in .NET Framework 4.7.2 behoben wurden.</span><span class="sxs-lookup"><span data-stu-id="d94f5-208">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="d94f5-209">Sie können die Erfahrung für Benutzer verbessern, die .NET Framework 4.6.1 über 4.7.1 verwenden, indem Sie .NET Framework 4.6.1 als Ziel festlegen.</span><span class="sxs-lookup"><span data-stu-id="d94f5-209">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="d94f5-210">Wenn Ihre Bibliothek plattformspezifische APIs aufrufen muss, legen Sie spezifische .NET-Implementierungen anstelle von .NET Standard als Ziel fest.</span><span class="sxs-lookup"><span data-stu-id="d94f5-210">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="d94f5-211">Weitere Informationen finden Sie unter [Multi-Targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span><span class="sxs-lookup"><span data-stu-id="d94f5-211">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="d94f5-212">Verwenden einer API, die nicht geändert wurde</span><span class="sxs-lookup"><span data-stu-id="d94f5-212">Use an API that hasn't changed</span></span>

<span data-ttu-id="d94f5-213">Stellen Sie sich ein Szenario vor, in dem Sie ein Upgrade einer Middleware-Bibliothek von .NET Core 2.2 auf 3.0 durchführen.</span><span class="sxs-lookup"><span data-stu-id="d94f5-213">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="d94f5-214">Die Middleware-APIs von ASP.NET Core, die in der Bibliothek verwendet werden, haben sich zwischen ASP.NET Core 2.2 und 3.0 nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="d94f5-214">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="d94f5-215">Um die Middleware-Bibliothek in .NET Core 3.0 weiterhin zu unterstützen, führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="d94f5-215">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="d94f5-216">Befolgen Sie den [Standardbibliotheksleitfaden](/dotnet/standard/library-guidance/).</span><span class="sxs-lookup"><span data-stu-id="d94f5-216">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="d94f5-217">Fügen Sie einen Paketverweis für das NuGet-Paket jeder API hinzu, wenn die entsprechende Assembly im freigegebenen Framework nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d94f5-217">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="d94f5-218">Verwenden einer API, die geändert wurde</span><span class="sxs-lookup"><span data-stu-id="d94f5-218">Use an API that changed</span></span>

<span data-ttu-id="d94f5-219">Stellen Sie sich ein Szenario vor, in dem Sie ein Upgrade einer Bibliothek von .NET Core 2.2 auf .NET Core 3.0 durchführen.</span><span class="sxs-lookup"><span data-stu-id="d94f5-219">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="d94f5-220">Eine ASP.NET Core-API, die in der Bibliothek verwendet wird, weist einen [Breaking Change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0 auf.</span><span class="sxs-lookup"><span data-stu-id="d94f5-220">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="d94f5-221">Überprüfen Sie, ob die Bibliothek so umgeschrieben werden kann, dass sie die nicht funktionale API in allen Versionen nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="d94f5-221">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="d94f5-222">Wenn Sie die Bibliothek umschreiben können, tun Sie dies, und legen Sie weiterhin ein früheres Zielframework mit Paketverweisen fest (z. B. .NET Standard 2.0 oder .NET Framework 4.6.1).</span><span class="sxs-lookup"><span data-stu-id="d94f5-222">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="d94f5-223">Wenn die Bibliothek nicht umgeschrieben werden kann, führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="d94f5-223">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="d94f5-224">Fügen Sie ein Ziel für .NET Core 3.0 hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-224">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="d94f5-225">Fügen Sie ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-225">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="d94f5-226">Verwenden Sie die [#if-Präprozessordirektive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) mit dem entsprechenden Zielframeworksymbol, um Code bedingt zu kompilieren.</span><span class="sxs-lookup"><span data-stu-id="d94f5-226">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="d94f5-227">Beispielsweise sind synchrone Lese- und Schreibvorgänge in HTTP-Anforderungs- und-Antwortdatenströmen standardmäßig ab ASP.NET Core 3.0 deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="d94f5-227">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="d94f5-228">ASP.NET Core 2.2 unterstützt das synchrone Verhalten standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="d94f5-228">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="d94f5-229">Stellen Sie sich eine Middleware-Bibliothek vor, in der synchrone Lese- und Schreibvorgänge aktiviert werden sollen, wenn E/A auftritt.</span><span class="sxs-lookup"><span data-stu-id="d94f5-229">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="d94f5-230">Die Bibliothek sollte den Code einschließen, um in der entsprechenden Präprozessordirektive synchrone Funktionen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="d94f5-230">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="d94f5-231">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d94f5-231">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="d94f5-232">Verwenden einer in 3.0 eingeführten API</span><span class="sxs-lookup"><span data-stu-id="d94f5-232">Use an API introduced in 3.0</span></span>

<span data-ttu-id="d94f5-233">Stellen Sie sich vor, dass Sie eine ASP.NET Core-API verwenden möchten, die in ASP.NET Core 3.0 eingeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="d94f5-233">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="d94f5-234">Stellen Sie sich die folgenden Fragen:</span><span class="sxs-lookup"><span data-stu-id="d94f5-234">Consider the following questions:</span></span>

1. <span data-ttu-id="d94f5-235">Erfordert die Bibliothek funktionell die neue API?</span><span class="sxs-lookup"><span data-stu-id="d94f5-235">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="d94f5-236">Kann dieses Feature von der Bibliothek auf andere Weise implementiert werden?</span><span class="sxs-lookup"><span data-stu-id="d94f5-236">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="d94f5-237">Wenn die Bibliothek funktionell die API erfordert und es keine Möglichkeit gibt, sie auf einer Unterebene zu implementieren:</span><span class="sxs-lookup"><span data-stu-id="d94f5-237">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="d94f5-238">Legen Sie .NET Core 3.x als alleiniges Ziel fest.</span><span class="sxs-lookup"><span data-stu-id="d94f5-238">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="d94f5-239">Fügen Sie ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-239">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="d94f5-240">Falls dieses Feature von der Bibliothek auf andere Weise implementiert werden kann:</span><span class="sxs-lookup"><span data-stu-id="d94f5-240">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="d94f5-241">Fügen Sie .NET Core 3.x als Zielframework hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-241">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="d94f5-242">Fügen Sie ein `<FrameworkReference>`-Element für das freigegebene Framework hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-242">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="d94f5-243">Verwenden Sie die [#if-Präprozessordirektive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) mit dem entsprechenden Zielframeworksymbol, um Code bedingt zu kompilieren.</span><span class="sxs-lookup"><span data-stu-id="d94f5-243">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="d94f5-244">Das folgende Taghilfsprogramm verwendet beispielsweise die in ASP.NET Core 3.0 eingeführte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="d94f5-244">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="d94f5-245">Consumer mit .NET Core 3.0 als Ziel führen den durch das `NETCOREAPP3_0`-Zielframeworksymbol definierten Codepfad aus.</span><span class="sxs-lookup"><span data-stu-id="d94f5-245">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="d94f5-246">Der Konstruktorparametertyp des Taghilfsprogramms ändert sich für .NET Core 2.1- und .NET Framework 4.6.1-Consumer in <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>.</span><span class="sxs-lookup"><span data-stu-id="d94f5-246">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="d94f5-247">Diese Änderung war erforderlich, weil in ASP.NET Core 3.0 `IHostingEnvironment` als veraltet markiert und `IWebHostEnvironment` als Ersatz empfohlen wurde.</span><span class="sxs-lookup"><span data-stu-id="d94f5-247">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="d94f5-248">Die folgende Projektdatei für mehrere Ziele unterstützt dieses Szenario eines Taghilfsprogramms:</span><span class="sxs-lookup"><span data-stu-id="d94f5-248">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="d94f5-249">Verwenden einer aus dem freigegebenen Framework entfernten API</span><span class="sxs-lookup"><span data-stu-id="d94f5-249">Use an API removed from the shared framework</span></span>

<span data-ttu-id="d94f5-250">Wenn Sie eine ASP.NET Core-Assembly verwenden möchten, die aus dem freigegebenen Framework entfernt wurde, fügen Sie den entsprechenden Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="d94f5-250">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="d94f5-251">Eine Liste der Pakete, die in ASP.NET Core 3.0 aus dem freigegebenen Framework entfernt wurden, finden Sie unter [Entfernen von Verweisen auf veraltete Pakete](xref:migration/22-to-30#remove-obsolete-package-references).</span><span class="sxs-lookup"><span data-stu-id="d94f5-251">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="d94f5-252">So fügen Sie z. B. den Web-API-Client hinzu:</span><span class="sxs-lookup"><span data-stu-id="d94f5-252">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="d94f5-253">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d94f5-253">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [<span data-ttu-id="d94f5-254">Unterstützung der .NET-Implementierung</span><span class="sxs-lookup"><span data-stu-id="d94f5-254">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="d94f5-255">.NET-Unterstützungsrichtlinien</span><span class="sxs-lookup"><span data-stu-id="d94f5-255">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
