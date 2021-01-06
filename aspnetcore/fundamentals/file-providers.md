---
title: Dateianbieter in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core Dateisystemzugriff durch die Verwendung von Dateianbietern abstrahiert.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
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
uid: fundamentals/file-providers
ms.openlocfilehash: 16e5ead9898125c804da4d60322510474201d897
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059441"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="a35b0-103">Dateianbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a35b0-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="a35b0-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a35b0-104">By [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a35b0-105">ASP.NET Core abstrahiert Dateisystemzugriff durch die Verwendung von Dateianbietern.</span><span class="sxs-lookup"><span data-stu-id="a35b0-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="a35b0-106">Dateianbieter werden im gesamten ASP.NET Core-Framework verwendet.</span><span class="sxs-lookup"><span data-stu-id="a35b0-106">File Providers are used throughout the ASP.NET Core framework.</span></span> <span data-ttu-id="a35b0-107">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a35b0-107">For example:</span></span>

* <span data-ttu-id="a35b0-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> macht das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root) der App als `IFileProvider`-Typen verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a35b0-108"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="a35b0-109">Die [Middleware der statischen Dateien](xref:fundamentals/static-files) verwendet Dateianbieter, um statische Dateien zu suchen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-109">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="a35b0-110">[Razor](xref:mvc/views/razor) verwendet Dateianbieter, um Seiten und Ansichten zu suchen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-110">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="a35b0-111">.NET Core-Tools verwenden Dateianbieter und Globmuster, um anzugeben, welche Dateien veröffentlicht werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-111">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="a35b0-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a35b0-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="a35b0-113">Dateianbieterschnittstellen</span><span class="sxs-lookup"><span data-stu-id="a35b0-113">File Provider interfaces</span></span>

<span data-ttu-id="a35b0-114">Die primäre Schnittstelle ist <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="a35b0-114">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="a35b0-115">`IFileProvider` stellt Methoden zur Verfügung, zum:</span><span class="sxs-lookup"><span data-stu-id="a35b0-115">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="a35b0-116">Abrufen von Dateiinformationen (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="a35b0-116">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="a35b0-117">Abrufen von Verzeichnisinformationen (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="a35b0-117">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="a35b0-118">Einrichten von Änderungsbenachrichtigungen (mithilfe eines <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="a35b0-118">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="a35b0-119">`IFileInfo` stellt Methoden und Eigenschaften für die Arbeit mit Dateien bereit:</span><span class="sxs-lookup"><span data-stu-id="a35b0-119">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="a35b0-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in Bytes)</span><span class="sxs-lookup"><span data-stu-id="a35b0-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="a35b0-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> – ein Datum</span><span class="sxs-lookup"><span data-stu-id="a35b0-121"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="a35b0-122">Sie können die Datei mithilfe der <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType>-Methode lesen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-122">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="a35b0-123">Die Beispiel-App *FileProviderSample* demonstriert die Konfiguration eines Dateianbieters in `Startup.ConfigureServices` für die Verwendung in der gesamten App über [Dependency Injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a35b0-123">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="a35b0-124">Dateianbieterimplementierungen</span><span class="sxs-lookup"><span data-stu-id="a35b0-124">File Provider implementations</span></span>

<span data-ttu-id="a35b0-125">In der folgenden Tabelle werden Implementierungen von `IFileProvider` aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="a35b0-125">The following table lists implementations of `IFileProvider`.</span></span>

| <span data-ttu-id="a35b0-126">Implementierung</span><span class="sxs-lookup"><span data-stu-id="a35b0-126">Implementation</span></span> | <span data-ttu-id="a35b0-127">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a35b0-127">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="a35b0-128">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-128">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="a35b0-129">Diese Implementierung wird verwendet, um kombinierten Zugriff auf Dateien und Verzeichnisse von mindestens einem anderen Anbieter bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-129">Used to provide combined access to files and directories from one or more other providers.</span></span> |
| [<span data-ttu-id="a35b0-130">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-130">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="a35b0-131">Diese Implementierung wird für den Zugriff auf in Assemblys eingebettete Dateien verwendet.</span><span class="sxs-lookup"><span data-stu-id="a35b0-131">Used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="a35b0-132">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-132">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="a35b0-133">Diese Implementierung wird für den Zugriff auf die physischen Dateien des Systems verwendet.</span><span class="sxs-lookup"><span data-stu-id="a35b0-133">Used to access the system's physical files.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="a35b0-134">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-134">PhysicalFileProvider</span></span>

<span data-ttu-id="a35b0-135">Der <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> ermöglicht den Zugriff auf das physische Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="a35b0-135">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="a35b0-136">`PhysicalFileProvider` verwendet den <xref:System.IO.File?displayProperty=fullName>-Typ (für den physischen Anbieter). Alle Pfade werden einem Verzeichnis und dessen untergeordneten Elementen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a35b0-136">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="a35b0-137">Diese Zuordnung verhindert den Zugriff auf das Dateisystem außerhalb des angegebenen Verzeichnisses und den untergeordneten Elementen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-137">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="a35b0-138">Das häufigste Szenario, in dem ein `PhysicalFileProvider` erstellt und verwendet wird, ist die Anforderung eines `IFileProvider` in einem Konstruktor über eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a35b0-138">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="a35b0-139">Beim direkten Instanziieren dieses Anbieters ist ein absoluter Verzeichnispfad erforderlich, der als Basispfad für alle Anforderungen über diesen Anbieter dient.</span><span class="sxs-lookup"><span data-stu-id="a35b0-139">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="a35b0-140">Globmuster werden im Verzeichnispfad nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a35b0-140">Glob patterns aren't supported in the directory path.</span></span>

<span data-ttu-id="a35b0-141">Im folgenden Code wird die Verwendung von `PhysicalFileProvider` zum Abrufen von Verzeichnisinhalten und Dateiinformationen veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="a35b0-141">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="a35b0-142">Typen im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a35b0-142">Types in the preceding example:</span></span>

* <span data-ttu-id="a35b0-143">`provider` ist ein `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="a35b0-143">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="a35b0-144">`contents` ist ein `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="a35b0-144">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="a35b0-145">`fileInfo` ist ein `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="a35b0-145">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="a35b0-146">Der Dateianbieter kann verwendet werden, um das durch `applicationRoot` angegebene Verzeichnis zu durchlaufen oder `GetFileInfo` aufzurufen, um die Informationen einer Datei anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-146">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="a35b0-147">Globmuster können nicht an die `GetFileInfo`-Methode übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="a35b0-147">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="a35b0-148">Der Dateianbieter hat keinen Zugriff außerhalb des `applicationRoot`-Verzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="a35b0-148">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="a35b0-149">Die Beispiel-App *FileProviderSample* erstellt den Anbieter in der `Startup.ConfigureServices`-Methode mithilfe von <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="a35b0-149">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="a35b0-150">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-150">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="a35b0-151">Der <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.</span><span class="sxs-lookup"><span data-stu-id="a35b0-151">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="a35b0-152">Der `ManifestEmbeddedFileProvider` verwendet ein in die Assembly kompiliertes Manifest, um die ursprünglichen Pfade der eingebetteten Dateien zu rekonstruieren.</span><span class="sxs-lookup"><span data-stu-id="a35b0-152">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="a35b0-153">So generieren Sie ein Manifest der eingebetteten Dateien:</span><span class="sxs-lookup"><span data-stu-id="a35b0-153">To generate a manifest of the embedded files:</span></span>

1. <span data-ttu-id="a35b0-154">Fügen Sie das NuGet-Paket [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) zu Ihrem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="a35b0-154">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>
1. <span data-ttu-id="a35b0-155">Legen Sie die `<GenerateEmbeddedFilesManifest>` -Eigenschaft auf `true`fest.</span><span class="sxs-lookup"><span data-stu-id="a35b0-155">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="a35b0-156">Geben Sie die einzubettenden Dateien mit [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) an:</span><span class="sxs-lookup"><span data-stu-id="a35b0-156">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="a35b0-157">Verwenden Sie [Globmuster](#glob-patterns), um eine oder mehr Dateien anzugeben, die in die Assembly eingebettet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-157">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="a35b0-158">Die Beispiel-App *FileProviderSample* erstellt einen `ManifestEmbeddedFileProvider` und übergibt die aktuell ausgeführte Assembly an den Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="a35b0-158">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="a35b0-159">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a35b0-159">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="a35b0-160">Mit zusätzlichen Überladungen können Sie:</span><span class="sxs-lookup"><span data-stu-id="a35b0-160">Additional overloads allow you to:</span></span>

* <span data-ttu-id="a35b0-161">Einen relativen Dateipfad angeben.</span><span class="sxs-lookup"><span data-stu-id="a35b0-161">Specify a relative file path.</span></span>
* <span data-ttu-id="a35b0-162">Dateien einem zuletzt geänderten Datum zuordnen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-162">Scope files to a last modified date.</span></span>
* <span data-ttu-id="a35b0-163">Die eingebettete Ressource benennen, die das eingebettete Dateimanifest enthält.</span><span class="sxs-lookup"><span data-stu-id="a35b0-163">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="a35b0-164">Überladung</span><span class="sxs-lookup"><span data-stu-id="a35b0-164">Overload</span></span> | <span data-ttu-id="a35b0-165">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a35b0-165">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="a35b0-166">Akzeptiert einen optionalen relativen `root`-Pfadparameter.</span><span class="sxs-lookup"><span data-stu-id="a35b0-166">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="a35b0-167">Geben Sie `root` an, um Aufrufe von <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> auf die Ressourcen im angegebenen Pfad zu begrenzen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-167">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="a35b0-168">Akzeptiert einen optionalen relativen `root`-Pfadparameter und einen Datumsparameter für `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="a35b0-168">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="a35b0-169">Das `lastModified`-Datum ordnet das letzte Änderungsdatum für die <xref:Microsoft.Extensions.FileProviders.IFileInfo>-Instanzen zu, die vom <xref:Microsoft.Extensions.FileProviders.IFileProvider> zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="a35b0-169">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="a35b0-170">Akzeptiert einen optionalen, relativen `root`-Pfad, ein `lastModified`-Datum und `manifestName`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="a35b0-170">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="a35b0-171">`manifestName` stellt den Namen der eingebetteten Ressource dar, die das Manifest enthält.</span><span class="sxs-lookup"><span data-stu-id="a35b0-171">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="a35b0-172">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-172">CompositeFileProvider</span></span>

<span data-ttu-id="a35b0-173">Der <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombiniert `IFileProvider`-Instanzen, die eine einzelne Schnittstelle zum Arbeiten mit Dateien von mehreren Anbietern verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-173">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="a35b0-174">Beim Erstellen des `CompositeFileProvider` übergeben Sie eine oder mehrere `IFileProvider`-Instanzen an seinen Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="a35b0-174">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="a35b0-175">In der Beispiel-App *FileProviderSample* stellen die Anbieter `PhysicalFileProvider` und `ManifestEmbeddedFileProvider` Dateien für einen `CompositeFileProvider`-Anbieter bereit, der im Dienstcontainer der App registriert ist.</span><span class="sxs-lookup"><span data-stu-id="a35b0-175">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span> <span data-ttu-id="a35b0-176">Der folgende Code befindet sich in der `Startup.ConfigureServices`-Methode des Projekts:</span><span class="sxs-lookup"><span data-stu-id="a35b0-176">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="a35b0-177">Überwachen auf Änderungen</span><span class="sxs-lookup"><span data-stu-id="a35b0-177">Watch for changes</span></span>

<span data-ttu-id="a35b0-178">Die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType>-Methode bietet ein Szenario zum Überwachen von Änderungen an Dateien oder Verzeichnissen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-178">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="a35b0-179">Die `Watch`-Methode:</span><span class="sxs-lookup"><span data-stu-id="a35b0-179">The `Watch` method:</span></span>

* <span data-ttu-id="a35b0-180">akzeptiert eine Dateipfadzeichenfolge, die [Globmuster](#glob-patterns) verwenden kann, um mehrere Dateien anzugeben.</span><span class="sxs-lookup"><span data-stu-id="a35b0-180">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span>
* <span data-ttu-id="a35b0-181">Gibt einen <xref:Microsoft.Extensions.Primitives.IChangeToken> zurück.</span><span class="sxs-lookup"><span data-stu-id="a35b0-181">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span>

<span data-ttu-id="a35b0-182">Das resultierende Änderungstoken stellt Folgendes zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="a35b0-182">The resulting change token exposes:</span></span>

* <span data-ttu-id="a35b0-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Eine Eigenschaft, die überprüft werden kann, um festzustellen, ob eine Änderung vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="a35b0-183"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="a35b0-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Diese Methode wird aufgerufen, wenn Änderungen an der angegebenen Pfadzeichenfolge erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="a35b0-184"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="a35b0-185">Jedes Änderungstoken ruft nur seine zugeordneten Rückrufe als Antwort auf eine einzelne Änderung auf.</span><span class="sxs-lookup"><span data-stu-id="a35b0-185">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="a35b0-186">Zur Aktivierung einer konstanten Überwachung verwenden Sie eine <xref:System.Threading.Tasks.TaskCompletionSource`1> (wie unten dargestellt), oder erstellen Sie `IChangeToken`-Instanzen nach Änderungen neu.</span><span class="sxs-lookup"><span data-stu-id="a35b0-186">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="a35b0-187">Die Beispiel-App *WatchConsole* schreibt eine Nachricht, wenn eine *TXT*-Datei im Verzeichnis *TextFiles* geändert wird:</span><span class="sxs-lookup"><span data-stu-id="a35b0-187">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="a35b0-188">Einige Dateisysteme, z.B. Docker-Container und Netzwerkfreigaben, senden Änderungsmeldungen möglicherweise nicht zuverlässig .</span><span class="sxs-lookup"><span data-stu-id="a35b0-188">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="a35b0-189">Legen Sie die `DOTNET_USE_POLLING_FILE_WATCHER`-Umgebungsvariable auf `1` oder `true` fest, um das Dateisystem alle 4 Sekunden nach Änderungen zu untersuchen (nicht konfigurierbar).</span><span class="sxs-lookup"><span data-stu-id="a35b0-189">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="a35b0-190">Globmuster</span><span class="sxs-lookup"><span data-stu-id="a35b0-190">Glob patterns</span></span>

<span data-ttu-id="a35b0-191">Dateisystempfade verwenden Platzhaltermuster namens *Globmuster*.</span><span class="sxs-lookup"><span data-stu-id="a35b0-191">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="a35b0-192">Geben Sie Gruppen von Dateien mit diesen Mustern an.</span><span class="sxs-lookup"><span data-stu-id="a35b0-192">Specify groups of files with these patterns.</span></span> <span data-ttu-id="a35b0-193">Die zwei Platzhalterzeichen sind `*` und `**`:</span><span class="sxs-lookup"><span data-stu-id="a35b0-193">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="a35b0-194">Überprüft alles auf der aktuellen Ordnerebene, jeden Dateinamen oder jede Dateierweiterung.</span><span class="sxs-lookup"><span data-stu-id="a35b0-194">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="a35b0-195">Übereinstimmungen werden durch `/`- und `.`-Zeichen im Dateipfad beendet.</span><span class="sxs-lookup"><span data-stu-id="a35b0-195">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="a35b0-196">Überprüft alles auf mehrere Verzeichnisebenen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-196">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="a35b0-197">Kann verwendet werden, um rekursiv eine Übereinstimmung für viele Dateien innerhalb einer Verzeichnishierarchie zu finden.</span><span class="sxs-lookup"><span data-stu-id="a35b0-197">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="a35b0-198">Die folgende Tabelle enthält gängige Beispiele für Globmuster.</span><span class="sxs-lookup"><span data-stu-id="a35b0-198">The following table provides common examples of glob patterns.</span></span>

|<span data-ttu-id="a35b0-199">Muster</span><span class="sxs-lookup"><span data-stu-id="a35b0-199">Pattern</span></span>  |<span data-ttu-id="a35b0-200">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a35b0-200">Description</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="a35b0-201">Entspricht einer bestimmten Datei in einem bestimmten Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="a35b0-201">Matches a specific file in a specific directory.</span></span>|
|`directory/*.txt`|<span data-ttu-id="a35b0-202">Entspricht allen Dateien mit *.txt*-Erweiterung in einem bestimmten Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="a35b0-202">Matches all files with *.txt* extension in a specific directory.</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="a35b0-203">Dieses Muster entspricht allen *appsettings.json* -Dateien in Verzeichnissen auf der nächsttieferen Ebene des *directory*-Ordners.</span><span class="sxs-lookup"><span data-stu-id="a35b0-203">Matches all *appsettings.json* files in directories exactly one level below the *directory* folder.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="a35b0-204">Vergleicht alle Dateien mit der Erweiterung *.txt*, die innerhalb des Ordners *directory* ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="a35b0-204">Matches all files with a *.txt* extension found anywhere under the *directory* folder.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a35b0-205">ASP.NET Core abstrahiert Dateisystemzugriff durch die Verwendung von Dateianbietern.</span><span class="sxs-lookup"><span data-stu-id="a35b0-205">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="a35b0-206">Dateianbieter werden im gesamten ASP.NET Core-Framework verwendet:</span><span class="sxs-lookup"><span data-stu-id="a35b0-206">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="a35b0-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> macht das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root) der App als `IFileProvider`-Typen verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a35b0-207"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>
* <span data-ttu-id="a35b0-208">Die [Middleware der statischen Dateien](xref:fundamentals/static-files) verwendet Dateianbieter, um statische Dateien zu suchen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-208">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="a35b0-209">[Razor](xref:mvc/views/razor) verwendet Dateianbieter, um Seiten und Ansichten zu suchen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-209">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="a35b0-210">.NET Core-Tools verwenden Dateianbieter und Globmuster, um anzugeben, welche Dateien veröffentlicht werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-210">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="a35b0-211">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a35b0-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="a35b0-212">Dateianbieterschnittstellen</span><span class="sxs-lookup"><span data-stu-id="a35b0-212">File Provider interfaces</span></span>

<span data-ttu-id="a35b0-213">Die primäre Schnittstelle ist <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="a35b0-213">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="a35b0-214">`IFileProvider` stellt Methoden zur Verfügung, zum:</span><span class="sxs-lookup"><span data-stu-id="a35b0-214">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="a35b0-215">Abrufen von Dateiinformationen (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="a35b0-215">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="a35b0-216">Abrufen von Verzeichnisinformationen (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="a35b0-216">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="a35b0-217">Einrichten von Änderungsbenachrichtigungen (mithilfe eines <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="a35b0-217">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="a35b0-218">`IFileInfo` stellt Methoden und Eigenschaften für die Arbeit mit Dateien bereit:</span><span class="sxs-lookup"><span data-stu-id="a35b0-218">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="a35b0-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in Bytes)</span><span class="sxs-lookup"><span data-stu-id="a35b0-219"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="a35b0-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> – ein Datum</span><span class="sxs-lookup"><span data-stu-id="a35b0-220"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="a35b0-221">Mithilfe der [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*)-Methode können Sie die Datei auslesen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-221">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="a35b0-222">Die Beispiel-App demonstriert die Konfiguration eines Dateianbieters in `Startup.ConfigureServices` für die Verwendung in der gesamten App über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a35b0-222">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="a35b0-223">Dateianbieterimplementierungen</span><span class="sxs-lookup"><span data-stu-id="a35b0-223">File Provider implementations</span></span>

<span data-ttu-id="a35b0-224">Es sind drei Implementierungen von `IFileProvider` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a35b0-224">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="a35b0-225">Implementierung</span><span class="sxs-lookup"><span data-stu-id="a35b0-225">Implementation</span></span> | <span data-ttu-id="a35b0-226">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a35b0-226">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="a35b0-227">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-227">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="a35b0-228">Der physische Anbieter wird verwendet, um auf die physischen Systemdateien zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-228">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="a35b0-229">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-229">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="a35b0-230">Der eingebettete Manifesanbieter wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.</span><span class="sxs-lookup"><span data-stu-id="a35b0-230">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="a35b0-231">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-231">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="a35b0-232">Der zusammengesetzte Anbieter wird verwendet, um kombinierten Zugriff auf Dateien und Verzeichnisse von einem oder mehreren anderen Anbietern bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-232">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="a35b0-233">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-233">PhysicalFileProvider</span></span>

<span data-ttu-id="a35b0-234">Der <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> ermöglicht den Zugriff auf das physische Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="a35b0-234">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="a35b0-235">`PhysicalFileProvider` verwendet den <xref:System.IO.File?displayProperty=fullName>-Typ (für den physischen Anbieter). Alle Pfade werden einem Verzeichnis und dessen untergeordneten Elementen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="a35b0-235">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="a35b0-236">Diese Zuordnung verhindert den Zugriff auf das Dateisystem außerhalb des angegebenen Verzeichnisses und den untergeordneten Elementen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-236">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="a35b0-237">Das häufigste Szenario, in dem ein `PhysicalFileProvider` erstellt und verwendet wird, ist die Anforderung eines `IFileProvider` in einem Konstruktor über eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="a35b0-237">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="a35b0-238">Beim direkten Instanziieren dieses Anbieters ist ein Verzeichnispfad erforderlich, der als Basispfad für alle Anforderungen über diesen Anbieter dient.</span><span class="sxs-lookup"><span data-stu-id="a35b0-238">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="a35b0-239">Der folgende Code zeigt die Erstellung eines `PhysicalFileProvider` und dessen Verwendung zum Abrufen von Verzeichnisinhalten und Dateiinformationen:</span><span class="sxs-lookup"><span data-stu-id="a35b0-239">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="a35b0-240">Typen im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a35b0-240">Types in the preceding example:</span></span>

* <span data-ttu-id="a35b0-241">`provider` ist ein `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="a35b0-241">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="a35b0-242">`contents` ist ein `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="a35b0-242">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="a35b0-243">`fileInfo` ist ein `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="a35b0-243">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="a35b0-244">Der Dateianbieter kann verwendet werden, um das durch `applicationRoot` angegebene Verzeichnis zu durchlaufen oder `GetFileInfo` aufzurufen, um die Informationen einer Datei anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-244">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="a35b0-245">Der Dateianbieter hat keinen Zugriff außerhalb des `applicationRoot`-Verzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="a35b0-245">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="a35b0-246">Die Beispiel-App erstellt einen Anbieter in `Startup.ConfigureServices`-Klasse der App mit [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="a35b0-246">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="a35b0-247">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-247">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="a35b0-248">Der <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.</span><span class="sxs-lookup"><span data-stu-id="a35b0-248">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="a35b0-249">Der `ManifestEmbeddedFileProvider` verwendet ein in die Assembly kompiliertes Manifest, um die ursprünglichen Pfade der eingebetteten Dateien zu rekonstruieren.</span><span class="sxs-lookup"><span data-stu-id="a35b0-249">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="a35b0-250">Um ein Manifest der eingebetteten Dateien zu generieren, legen die `<GenerateEmbeddedFilesManifest>`-Eigenschaft auf `true` fest.</span><span class="sxs-lookup"><span data-stu-id="a35b0-250">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="a35b0-251">Geben Sie die einzubettenden Dateien mit [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) an:</span><span class="sxs-lookup"><span data-stu-id="a35b0-251">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-xml[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="a35b0-252">Verwenden Sie [Globmuster](#glob-patterns), um eine oder mehr Dateien anzugeben, die in die Assembly eingebettet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-252">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="a35b0-253">Die Beispiel-App erstellt einen `ManifestEmbeddedFileProvider` und übergibt die aktuell ausgeführte Assembly an den Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="a35b0-253">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="a35b0-254">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="a35b0-254">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="a35b0-255">Mit zusätzlichen Überladungen können Sie:</span><span class="sxs-lookup"><span data-stu-id="a35b0-255">Additional overloads allow you to:</span></span>

* <span data-ttu-id="a35b0-256">Einen relativen Dateipfad angeben.</span><span class="sxs-lookup"><span data-stu-id="a35b0-256">Specify a relative file path.</span></span>
* <span data-ttu-id="a35b0-257">Dateien einem zuletzt geänderten Datum zuordnen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-257">Scope files to a last modified date.</span></span>
* <span data-ttu-id="a35b0-258">Die eingebettete Ressource benennen, die das eingebettete Dateimanifest enthält.</span><span class="sxs-lookup"><span data-stu-id="a35b0-258">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="a35b0-259">Überladung</span><span class="sxs-lookup"><span data-stu-id="a35b0-259">Overload</span></span> | <span data-ttu-id="a35b0-260">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="a35b0-260">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="a35b0-261">Akzeptiert einen optionalen relativen `root`-Pfadparameter.</span><span class="sxs-lookup"><span data-stu-id="a35b0-261">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="a35b0-262">Geben Sie `root` an, um Aufrufe von <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> auf die Ressourcen im angegebenen Pfad zu begrenzen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-262">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="a35b0-263">Akzeptiert einen optionalen relativen `root`-Pfadparameter und einen Datumsparameter für `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="a35b0-263">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="a35b0-264">Das `lastModified`-Datum ordnet das letzte Änderungsdatum für die <xref:Microsoft.Extensions.FileProviders.IFileInfo>-Instanzen zu, die vom <xref:Microsoft.Extensions.FileProviders.IFileProvider> zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="a35b0-264">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="a35b0-265">Akzeptiert einen optionalen, relativen `root`-Pfad, ein `lastModified`-Datum und `manifestName`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="a35b0-265">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="a35b0-266">`manifestName` stellt den Namen der eingebetteten Ressource dar, die das Manifest enthält.</span><span class="sxs-lookup"><span data-stu-id="a35b0-266">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="a35b0-267">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="a35b0-267">CompositeFileProvider</span></span>

<span data-ttu-id="a35b0-268">Der <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombiniert `IFileProvider`-Instanzen, die eine einzelne Schnittstelle zum Arbeiten mit Dateien von mehreren Anbietern verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-268">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="a35b0-269">Beim Erstellen des `CompositeFileProvider` übergeben Sie eine oder mehrere `IFileProvider`-Instanzen an seinen Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="a35b0-269">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="a35b0-270">In der Beispiel-App stellt ein `PhysicalFileProvider` und ein `ManifestEmbeddedFileProvider` Dateien an ein `CompositeFileProvider`, der im Dienstcontainer der App registriert ist:</span><span class="sxs-lookup"><span data-stu-id="a35b0-270">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="a35b0-271">Überwachen auf Änderungen</span><span class="sxs-lookup"><span data-stu-id="a35b0-271">Watch for changes</span></span>

<span data-ttu-id="a35b0-272">Die [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)-Methode bietet ein Szenario, eine oder mehrere Dateien oder Verzeichnisse auf Änderungen zu überwachen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-272">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="a35b0-273">`Watch`akzeptiert eine Pfadzeichenfolge, die [Globmuster](#glob-patterns) verwenden kann, um mehrere Dateien anzugeben.</span><span class="sxs-lookup"><span data-stu-id="a35b0-273">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="a35b0-274"><xref:Microsoft.Extensions.Primitives.IChangeToken> gibt `Watch` zurück.</span><span class="sxs-lookup"><span data-stu-id="a35b0-274">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="a35b0-275">Das Änderungstoken macht folgendes verfügbar:</span><span class="sxs-lookup"><span data-stu-id="a35b0-275">The change token exposes:</span></span>

* <span data-ttu-id="a35b0-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Eine Eigenschaft, die überprüft werden kann, um festzustellen, ob eine Änderung vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="a35b0-276"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="a35b0-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Diese Methode wird aufgerufen, wenn Änderungen an der angegebenen Pfadzeichenfolge erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="a35b0-277"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="a35b0-278">Jedes Änderungstoken ruft nur seine zugeordneten Rückrufe als Antwort auf eine einzelne Änderung auf.</span><span class="sxs-lookup"><span data-stu-id="a35b0-278">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="a35b0-279">Zur Aktivierung einer konstanten Überwachung verwenden Sie eine <xref:System.Threading.Tasks.TaskCompletionSource`1> (wie unten dargestellt), oder erstellen Sie `IChangeToken`-Instanzen nach Änderungen neu.</span><span class="sxs-lookup"><span data-stu-id="a35b0-279">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="a35b0-280">In der Beispiel-App wird die *WatchConsole*-Konsolen-App konfiguriert, damit sie bei einer Änderung einer Textdatei eine Meldung anzeigt:</span><span class="sxs-lookup"><span data-stu-id="a35b0-280">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="a35b0-281">Einige Dateisysteme, z.B. Docker-Container und Netzwerkfreigaben, senden Änderungsmeldungen möglicherweise nicht zuverlässig .</span><span class="sxs-lookup"><span data-stu-id="a35b0-281">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="a35b0-282">Legen Sie die `DOTNET_USE_POLLING_FILE_WATCHER`-Umgebungsvariable auf `1` oder `true` fest, um das Dateisystem alle 4 Sekunden nach Änderungen zu untersuchen (nicht konfigurierbar).</span><span class="sxs-lookup"><span data-stu-id="a35b0-282">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="a35b0-283">Globmuster</span><span class="sxs-lookup"><span data-stu-id="a35b0-283">Glob patterns</span></span>

<span data-ttu-id="a35b0-284">Dateisystempfade verwenden Platzhaltermuster namens *Globmuster*.</span><span class="sxs-lookup"><span data-stu-id="a35b0-284">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="a35b0-285">Geben Sie Gruppen von Dateien mit diesen Mustern an.</span><span class="sxs-lookup"><span data-stu-id="a35b0-285">Specify groups of files with these patterns.</span></span> <span data-ttu-id="a35b0-286">Die zwei Platzhalterzeichen sind `*` und `**`:</span><span class="sxs-lookup"><span data-stu-id="a35b0-286">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="a35b0-287">Überprüft alles auf der aktuellen Ordnerebene, jeden Dateinamen oder jede Dateierweiterung.</span><span class="sxs-lookup"><span data-stu-id="a35b0-287">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="a35b0-288">Übereinstimmungen werden durch `/`- und `.`-Zeichen im Dateipfad beendet.</span><span class="sxs-lookup"><span data-stu-id="a35b0-288">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="a35b0-289">Überprüft alles auf mehrere Verzeichnisebenen.</span><span class="sxs-lookup"><span data-stu-id="a35b0-289">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="a35b0-290">Kann verwendet werden, um rekursiv eine Übereinstimmung für viele Dateien innerhalb einer Verzeichnishierarchie zu finden.</span><span class="sxs-lookup"><span data-stu-id="a35b0-290">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="a35b0-291">**Beispiele für Globmuster**</span><span class="sxs-lookup"><span data-stu-id="a35b0-291">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="a35b0-292">Entspricht einer bestimmten Datei in einem bestimmten Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="a35b0-292">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="a35b0-293">Entspricht allen Dateien mit *.txt*-Erweiterung in einem bestimmten Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="a35b0-293">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="a35b0-294">Entspricht allen `appsettings.json`-Dateien in Verzeichnissen auf der nächsttieferen Ebene des *directory*-Ordners.</span><span class="sxs-lookup"><span data-stu-id="a35b0-294">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="a35b0-295">Entspricht allen Dateien mit *.txt*-Erweiterung, die an einer beliebigen Stelle unter dem *directory*-Ordner gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="a35b0-295">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
