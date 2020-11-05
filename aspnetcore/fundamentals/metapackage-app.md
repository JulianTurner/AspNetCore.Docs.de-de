---
title: Microsoft.AspNetCore.App-Metapaket für ASP.NET Core
author: Rick-Anderson
description: Das freigegebene Microsoft.AspNetCore.App-Framework
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/24/2019
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
uid: fundamentals/metapackage-app
ms.openlocfilehash: 225bb1f55c099a476319191726c5a661f9a4893a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057803"
---
# <a name="microsoftaspnetcoreapp-for-aspnet-core"></a><span data-ttu-id="c8175-103">Microsoft.AspNetCore.App für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8175-103">Microsoft.AspNetCore.App for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

 <span data-ttu-id="c8175-104">Das freigegebene ASP.NET Core-Framework (`Microsoft.AspNetCore.App`) enthält Assemblys, die von Microsoft entwickelt und unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="c8175-104">The ASP.NET Core shared framework (`Microsoft.AspNetCore.App`) contains assemblies that are developed and supported by Microsoft.</span></span> <span data-ttu-id="c8175-105">`Microsoft.AspNetCore.App` wird bei der Installation des [SDK für .NET Core 3.0 oder höher](https://dotnet.microsoft.com/download/dotnet-core/3.0) installiert.</span><span class="sxs-lookup"><span data-stu-id="c8175-105">`Microsoft.AspNetCore.App` is installed when the [.NET Core 3.0 or later SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) is installed.</span></span> <span data-ttu-id="c8175-106">Das *freigegebene Framework* ist der Satz von Assemblys ( *DLL* -Dateien), die auf dem Computer installiert werden und eine Laufzeitkomponente sowie ein Zielpaket enthalten.</span><span class="sxs-lookup"><span data-stu-id="c8175-106">The *shared framework* is the set of assemblies ( *.dll* files) that are installed on the machine and includes a runtime component and a targeting pack.</span></span> <span data-ttu-id="c8175-107">Weitere Informationen finden Sie unter [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Das freigegebene Framework).</span><span class="sxs-lookup"><span data-stu-id="c8175-107">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

* <span data-ttu-id="c8175-108">Projekte, die auf das `Microsoft.NET.Sdk.Web` SDK abzielen, verweisen implizit auf das `Microsoft.AspNetCore.App`-Framework.</span><span class="sxs-lookup"><span data-stu-id="c8175-108">Projects that target the `Microsoft.NET.Sdk.Web` SDK implicitly reference the `Microsoft.AspNetCore.App` framework.</span></span>

<span data-ttu-id="c8175-109">Für diese Projekte sind keine weiteren Verweise erforderlich:</span><span class="sxs-lookup"><span data-stu-id="c8175-109">No additional references are required for these projects:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>
    ...
</Project>
```

<span data-ttu-id="c8175-110">Für das freigegebene ASP.NET Core-Framework gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c8175-110">The ASP.NET Core shared framework:</span></span>

* <span data-ttu-id="c8175-111">Es enthält keine Abhängigkeiten von Drittanbietern.</span><span class="sxs-lookup"><span data-stu-id="c8175-111">Doesn't include third-party dependencies.</span></span>
* <span data-ttu-id="c8175-112">Es enthält alle unterstützten Pakete des ASP.NET Core-Teams.</span><span class="sxs-lookup"><span data-stu-id="c8175-112">Includes all supported packages by the ASP.NET Core team.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c8175-113">Für dieses Feature ist ASP.NET Core 2.x für .NET Core 2.x erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c8175-113">This feature requires ASP.NET Core 2.x targeting .NET Core 2.x.</span></span>

<span data-ttu-id="c8175-114">Das [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App)-[Metapaket](/dotnet/core/packages#metapackages) für ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c8175-114">The [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App) [metapackage](/dotnet/core/packages#metapackages) for ASP.NET Core:</span></span>

* <span data-ttu-id="c8175-115">Enthält keine Drittanbieterabhängigkeiten außer [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/) und [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span><span class="sxs-lookup"><span data-stu-id="c8175-115">Does not include third-party dependencies except for [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/), [Remotion.Linq](https://www.nuget.org/packages/Remotion.Linq/), and [IX-Async](https://www.nuget.org/packages/System.Interactive.Async/).</span></span> <span data-ttu-id="c8175-116">Diese Drittanbieterabhängigkeiten sind erforderlich, damit die Features der wichtigsten Frameworks funktionieren.</span><span class="sxs-lookup"><span data-stu-id="c8175-116">These 3rd-party dependencies are deemed necessary to ensure the major frameworks features function.</span></span>
* <span data-ttu-id="c8175-117">Enthält alle unterstützten Pakete des ASP.NET Core-Teams außer denen, die Drittanbieterabhängigkeiten enthalten (von den bereits erwähnten abgesehen).</span><span class="sxs-lookup"><span data-stu-id="c8175-117">Includes all supported packages by the ASP.NET Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>
* <span data-ttu-id="c8175-118">Enthält alle unterstützten Pakete des Entity Framework Core-Teams außer denen, die Drittanbieterabhängigkeiten enthalten (von den bereits erwähnten abgesehen).</span><span class="sxs-lookup"><span data-stu-id="c8175-118">Includes all supported packages by the Entity Framework Core team except those that contain third-party dependencies (other than those previously mentioned).</span></span>

<span data-ttu-id="c8175-119">In dem Paket `Microsoft.AspNetCore.App` sind alle Features von ASP.NET Core 2.x und Entity Framework Core 2.x enthalten.</span><span class="sxs-lookup"><span data-stu-id="c8175-119">All the features of ASP.NET Core 2.x and Entity Framework Core 2.x are included in the `Microsoft.AspNetCore.App` package.</span></span> <span data-ttu-id="c8175-120">Die Standardprojektvorlagen für ASP.NET Core 2.x verwenden dieses Paket.</span><span class="sxs-lookup"><span data-stu-id="c8175-120">The default project templates targeting ASP.NET Core 2.x use this package.</span></span> <span data-ttu-id="c8175-121">Es wird empfohlen, dass Anwendungen für ASP.NET Core 2.x und Entity Framework Core 2.x das `Microsoft.AspNetCore.App`-Paket verwenden.</span><span class="sxs-lookup"><span data-stu-id="c8175-121">We recommend applications targeting ASP.NET Core 2.x and Entity Framework Core 2.x use the `Microsoft.AspNetCore.App` package.</span></span>

<span data-ttu-id="c8175-122">Die Versionsnummer des `Microsoft.AspNetCore.App`-Metapakets gibt die erforderlichen Mindestversionen von ASP.NET Core und Entity Framework Core an.</span><span class="sxs-lookup"><span data-stu-id="c8175-122">The version number of the `Microsoft.AspNetCore.App` metapackage represents the minimum ASP.NET Core version and Entity Framework Core version.</span></span>

<span data-ttu-id="c8175-123">Durch die Verwendung des Metapakets `Microsoft.AspNetCore.App` werden Versionseinschränkungen bereitgestellt, die Ihre App schützen:</span><span class="sxs-lookup"><span data-stu-id="c8175-123">Using the `Microsoft.AspNetCore.App` metapackage provides version restrictions that protect your app:</span></span>

* <span data-ttu-id="c8175-124">Wenn ein Paket enthalten wird, das eine transitive (nicht direkte) Abhängigkeit von einem Paket in `Microsoft.AspNetCore.App` besitzt und diese Versionsnummern sich unterscheiden, generiert NuGet einen Fehler.</span><span class="sxs-lookup"><span data-stu-id="c8175-124">If a package is included that has a transitive (not direct) dependency on a package in `Microsoft.AspNetCore.App`, and those version numbers differ, NuGet will generate an error.</span></span>
* <span data-ttu-id="c8175-125">Andere Pakete, die zu Ihrer App hinzugefügt werden, können die Version der in `Microsoft.AspNetCore.App` enthaltenen Pakete nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="c8175-125">Other packages added to your app cannot change the version of packages included in `Microsoft.AspNetCore.App`.</span></span>
* <span data-ttu-id="c8175-126">Die Konsistenz von Versionen stellt eine zuverlässige Verwendbarkeit sicher.</span><span class="sxs-lookup"><span data-stu-id="c8175-126">Version consistency ensures a reliable experience.</span></span> <span data-ttu-id="c8175-127">`Microsoft.AspNetCore.App` wurde dafür entwickelt, um nicht getestete Versionskombinationen von verwandten Komponenten zu verhindern, die in derselben App zusammen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c8175-127">`Microsoft.AspNetCore.App` was designed to prevent untested version combinations of related bits being used together in the same app.</span></span>

<span data-ttu-id="c8175-128">Anwendungen, die das Metapaket `Microsoft.AspNetCore.App` verwenden, profitieren automatisch vom freigegebenen ASP.NET Core-Framework.</span><span class="sxs-lookup"><span data-stu-id="c8175-128">Applications that use the `Microsoft.AspNetCore.App` metapackage automatically take advantage of the ASP.NET Core shared framework.</span></span> <span data-ttu-id="c8175-129">Bei Verwendung des Metapakets `Microsoft.AspNetCore.App` werden mit der Anwendung **keine** Objekte aus den referenzierten NuGet-Paketen für ASP.NET Core bereitgestellt. Das freigegebene ASP.NET Core-Framework enthält diese Objekte.</span><span class="sxs-lookup"><span data-stu-id="c8175-129">When you use the `Microsoft.AspNetCore.App` metapackage, **no** assets from the referenced ASP.NET Core NuGet packages are deployed with the application&mdash;the ASP.NET Core shared framework contains these assets.</span></span> <span data-ttu-id="c8175-130">Die Objekte im freigegebenen Framework sind zur Verbesserung der Startzeit der Anwendung vorkompiliert.</span><span class="sxs-lookup"><span data-stu-id="c8175-130">The assets in the shared framework are precompiled to improve application startup time.</span></span> <span data-ttu-id="c8175-131">Weitere Informationen finden Sie unter [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/) (Das freigegebene Framework).</span><span class="sxs-lookup"><span data-stu-id="c8175-131">For more information, see [The shared framework](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/).</span></span>

<span data-ttu-id="c8175-132">Die folgende Projektdatei verweist auf das Metapaket `Microsoft.AspNetCore.App` für ASP.NET Core und stellt eine typische ASP.NET Core 2.2-Vorlage dar:</span><span class="sxs-lookup"><span data-stu-id="c8175-132">The following project file references the `Microsoft.AspNetCore.App` metapackage for ASP.NET Core and represents a typical ASP.NET Core 2.2 template:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp2.2</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

</Project>
```

<span data-ttu-id="c8175-133">Das hier angegebene Markup stellt eine typische ASP.NET Core 2.x-Vorlage dar.</span><span class="sxs-lookup"><span data-stu-id="c8175-133">The preceding markup represents a typical ASP.NET Core 2.x template.</span></span> <span data-ttu-id="c8175-134">Es gibt keine Versionsnummer für den Paketverweis `Microsoft.AspNetCore.App` an.</span><span class="sxs-lookup"><span data-stu-id="c8175-134">It doesn't specify a version number for the `Microsoft.AspNetCore.App` package reference.</span></span> <span data-ttu-id="c8175-135">Wenn die Version nicht angegeben wird, wird vom SDK eine [implizite](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) Version angegeben (`Microsoft.NET.Sdk.Web`).</span><span class="sxs-lookup"><span data-stu-id="c8175-135">When the version is not specified, an [implicit](https://github.com/dotnet/core/blob/master/release-notes/1.0/sdk/1.0-rc3-implicit-package-refs.md) version is specified by the SDK, that is, `Microsoft.NET.Sdk.Web`.</span></span> <span data-ttu-id="c8175-136">Es wird empfohlen, die vom SDK angegebene implizite Version beizubehalten, statt die Versionsnummer im Paketverweis explizit festzulegen.</span><span class="sxs-lookup"><span data-stu-id="c8175-136">We recommend relying on the implicit version specified by the SDK and not explicitly setting the version number on the package reference.</span></span> <span data-ttu-id="c8175-137">Wenn Sie Fragen zu dieser Vorgehensweise haben, können Sie einen GitHub-Kommentar unter [Discussion for the Microsoft.AspNetCore.App implicit version (Diskussion zur impliziten Version für Microsoft.AspNetCore.App)](https://github.com/dotnet/AspNetCore.Docs/issues/6430) verfassen.</span><span class="sxs-lookup"><span data-stu-id="c8175-137">If you have questions on this approach, leave a GitHub comment at the [Discussion for the Microsoft.AspNetCore.App implicit version](https://github.com/dotnet/AspNetCore.Docs/issues/6430).</span></span>

<span data-ttu-id="c8175-138">Die implizite Version wird auf `major.minor.0` festgelegt, wenn es sich um Apps für Mobilgeräte handelt.</span><span class="sxs-lookup"><span data-stu-id="c8175-138">The implicit version is set to `major.minor.0` for portable apps.</span></span> <span data-ttu-id="c8175-139">Der Rollforwardmechanismus des freigegebenen Frameworks führt die App auf der neuesten kompatiblen Version der installierten freigegebenen Frameworks aus.</span><span class="sxs-lookup"><span data-stu-id="c8175-139">The shared framework roll-forward mechanism will run the app on the latest compatible version among the installed shared frameworks.</span></span> <span data-ttu-id="c8175-140">Stellen Sie sicher, dass die gleiche Version des freigegebenen Frameworks in allen Umgebungen installiert ist, um zu gewährleisten, dass die gleiche Version bei der Entwicklung, beim Testen und in der Produktion verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c8175-140">To guarantee the same version is used in development, test, and production, ensure the same version of the shared framework is installed in all environments.</span></span> <span data-ttu-id="c8175-141">Bei unabhängigen Apps wird die implizite Versionsnummer auf die Versionsnummer `major.minor.patch` des freigegebenen Frameworks festgelegt, das im installierten SDK zusammengefasst ist.</span><span class="sxs-lookup"><span data-stu-id="c8175-141">For self contained apps, the implicit version number is set to the `major.minor.patch` of the shared framework bundled in the installed SDK.</span></span>

<span data-ttu-id="c8175-142">Das Angeben einer Versionsnummer im `Microsoft.AspNetCore.App`-Verweis garantiert **nicht** , dass diese Version des freigegebenen Frameworks ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="c8175-142">Specifying a version number on the `Microsoft.AspNetCore.App` reference does **not** guarantee that version of the shared framework will be chosen.</span></span> <span data-ttu-id="c8175-143">Ein Beispiel: Version 2.2.1 ist angegeben, aber Version 2.2.3 ist installiert.</span><span class="sxs-lookup"><span data-stu-id="c8175-143">For example, suppose version "2.2.1" is specified, but "2.2.3" is installed.</span></span> <span data-ttu-id="c8175-144">In diesem Fall verwendet die App die Version 2.2.3.</span><span class="sxs-lookup"><span data-stu-id="c8175-144">In that case, the app will use "2.2.3".</span></span> <span data-ttu-id="c8175-145">Sie können den Rollforward (für „patch“ und/oder „minor“) deaktivieren. Dies wird jedoch nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="c8175-145">Although not recommended, you can disable roll forward (patch and/or minor).</span></span> <span data-ttu-id="c8175-146">Weitere Informationen zum Rollforward des dotnet-Hosts und der Konfiguration seines Verhaltens finden Sie unter [dotnet host roll forward (Rollforward des dotnet-Hosts)](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span><span class="sxs-lookup"><span data-stu-id="c8175-146">For more information regarding dotnet host roll-forward and how to configure its behavior, see [dotnet host roll forward](https://github.com/dotnet/core-setup/blob/master/Documentation/design-docs/roll-forward-on-no-candidate-fx.md).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="c8175-147">Für `<Project Sdk` muss `Microsoft.NET.Sdk.Web` festgelegt werden, um die implizite Version `Microsoft.AspNetCore.App` verwenden zu können.</span><span class="sxs-lookup"><span data-stu-id="c8175-147">`<Project Sdk` must be set to `Microsoft.NET.Sdk.Web` to use the implicit version `Microsoft.AspNetCore.App`.</span></span> <span data-ttu-id="c8175-148">Wenn `<Project Sdk="Microsoft.NET.Sdk">` (ohne nachgestelltes `.Web`) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="c8175-148">When `<Project Sdk="Microsoft.NET.Sdk">` (without the trailing `.Web`) is used:</span></span>

* <span data-ttu-id="c8175-149">Die folgende Warnung wird generiert:</span><span class="sxs-lookup"><span data-stu-id="c8175-149">The following warning is generated:</span></span>

  <span data-ttu-id="c8175-150">*Warnung NU1604: Projektabhängigkeit Microsoft.AspNetCore.App enthält keine inklusive Untergrenze. Schließen Sie eine Untergrenze in die Abhängigkeitsversion ein, um konsistente Wiederherstellungsergebnisse zu erzielen.)*</span><span class="sxs-lookup"><span data-stu-id="c8175-150">*Warning NU1604: Project dependency Microsoft.AspNetCore.App does not contain an inclusive lower bound. Include a lower bound in the dependency version to ensure consistent restore results.*</span></span>

* <span data-ttu-id="c8175-151">Dies ist ein bekanntes Problem mit dem .NET Core 2.1 SDK.</span><span class="sxs-lookup"><span data-stu-id="c8175-151">This is a known issue with the .NET Core 2.1 SDK.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<a name="update"></a>

## <a name="update-aspnet-core"></a><span data-ttu-id="c8175-152">Aktualisieren von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c8175-152">Update ASP.NET Core</span></span>

<span data-ttu-id="c8175-153">Das [Metapaket](/dotnet/core/packages#metapackages) `Microsoft.AspNetCore.App` zählt nicht zu den herkömmlichen Paketen, die über NuGet aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="c8175-153">The `Microsoft.AspNetCore.App` [metapackage](/dotnet/core/packages#metapackages) isn't a traditional package that's updated from NuGet.</span></span> <span data-ttu-id="c8175-154">Ähnlich wie bei `Microsoft.NETCore.App` stellt `Microsoft.AspNetCore.App` eine freigegebene Runtime dar, die über eine spezielle Semantik für die Versionsverwaltung verfügt, die außerhalb von NuGet behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="c8175-154">Similar to `Microsoft.NETCore.App`, `Microsoft.AspNetCore.App` represents a shared runtime, which has special versioning semantics handled outside of NuGet.</span></span> <span data-ttu-id="c8175-155">Weitere Informationen finden Sie unter [Pakete, Metapakete und Frameworks](/dotnet/core/packages).</span><span class="sxs-lookup"><span data-stu-id="c8175-155">For more information, see [Packages, metapackages and frameworks](/dotnet/core/packages).</span></span>

<span data-ttu-id="c8175-156">So aktualisieren Sie ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="c8175-156">To update ASP.NET Core:</span></span>

* <span data-ttu-id="c8175-157">Auf Entwicklungscomputern und Buildservern: Laden Sie das [.NET Core SDK](https://dotnet.microsoft.com/download) herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="c8175-157">On development machines and build servers: Download and install the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="c8175-158">Auf Bereitstellungsservern: Laden Sie die [.NET Core Runtime](https://dotnet.microsoft.com/download) herunter, und installieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="c8175-158">On deployment servers: Download and install the [.NET Core runtime](https://dotnet.microsoft.com/download).</span></span>

 <span data-ttu-id="c8175-159">Für Anwendungen wird beim Neustart der Anwendungen ein Rollforward auf die neueste installierte Version ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c8175-159">Applications will roll forward to the latest installed version on application restart.</span></span> <span data-ttu-id="c8175-160">Die Versionsnummer von `Microsoft.AspNetCore.App` muss in der Projektdatei nicht aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="c8175-160">It's not necessary to update the `Microsoft.AspNetCore.App` version number in the project file.</span></span> <span data-ttu-id="c8175-161">Weitere Informationen finden Sie unter [Von Frameworks abhängige Apps führen einen Rollforward aus](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span><span class="sxs-lookup"><span data-stu-id="c8175-161">For more information, see [Framework-dependent apps roll forward](/dotnet/core/versions/selection#framework-dependent-apps-roll-forward).</span></span>

<span data-ttu-id="c8175-162">Wenn Ihre Anwendung zuvor `Microsoft.AspNetCore.All` verwendet hat, finden Sie weitere Informationen unter [Migrieren von Microsoft.AspNetCore.All zu Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span><span class="sxs-lookup"><span data-stu-id="c8175-162">If your application previously used `Microsoft.AspNetCore.All`, see [Migrating from Microsoft.AspNetCore.All to Microsoft.AspNetCore.App](xref:fundamentals/metapackage#migrate).</span></span>

::: moniker-end
