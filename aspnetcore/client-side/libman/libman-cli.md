---
title: Verwenden der LibMan-CLI mit ASP.NET Core
author: scottaddie
description: Lernen Sie, wie Sie die LibMan-CLI in einem ASP.NET Core-Projekt verwenden können.
ms.author: scaddie
ms.custom: mvc
ms.date: 11/12/2019
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
uid: client-side/libman/libman-cli
ms.openlocfilehash: dad9136439b61ad98523061d181fe44d3bf1273d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054748"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a><span data-ttu-id="44b7b-103">Verwenden der LibMan-CLI mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="44b7b-103">Use the LibMan CLI with ASP.NET Core</span></span>

<span data-ttu-id="44b7b-104">Von [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="44b7b-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="44b7b-105">Die [LibMan](xref:client-side/libman/index)-CLI ist ein plattformübergreifendes Tool, das überall unterstützt wird, wo .NET Core unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="44b7b-105">The [LibMan](xref:client-side/libman/index) CLI is a cross-platform tool that's supported everywhere .NET Core is supported.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="44b7b-106">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="44b7b-106">Prerequisites</span></span>

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a><span data-ttu-id="44b7b-107">Installation</span><span class="sxs-lookup"><span data-stu-id="44b7b-107">Installation</span></span>

<span data-ttu-id="44b7b-108">Gehen Sie wie folgt vor, um die LibMan-CLI zu installieren:</span><span class="sxs-lookup"><span data-stu-id="44b7b-108">To install the LibMan CLI:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

<span data-ttu-id="44b7b-109">Ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools#install-a-global-tool) wird über das NuGet-Paket [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) installiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-109">A [.NET Core Global Tool](/dotnet/core/tools/global-tools#install-a-global-tool) is installed from the [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) NuGet package.</span></span>

<span data-ttu-id="44b7b-110">Gehen Sie wie folgt vor, um die LibMan-CLI aus einer bestimmten NuGet-Paketquelle zu installieren:</span><span class="sxs-lookup"><span data-stu-id="44b7b-110">To install the LibMan CLI from a specific NuGet package source:</span></span>

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

<span data-ttu-id="44b7b-111">Im obigen Beispiel wird mit der auf dem lokalen Windows-Computer gespeicherten Datei *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* ein globales .NET Core-Tool installiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-111">In the preceding example, a .NET Core Global Tool is installed from the local Windows machine's *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* file.</span></span>

## <a name="usage"></a><span data-ttu-id="44b7b-112">Verwendung</span><span class="sxs-lookup"><span data-stu-id="44b7b-112">Usage</span></span>

<span data-ttu-id="44b7b-113">Nach der erfolgreichen Installation der CLI kann folgender Befehl verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="44b7b-113">After successful installation of the CLI, the following command can be used:</span></span>

```console
libman
```

<span data-ttu-id="44b7b-114">Anzeigen der installierten Version der CLI:</span><span class="sxs-lookup"><span data-stu-id="44b7b-114">To view the installed CLI version:</span></span>

```console
libman --version
```

<span data-ttu-id="44b7b-115">Anzeigen der verfügbaren CLI-Befehle:</span><span class="sxs-lookup"><span data-stu-id="44b7b-115">To view the available CLI commands:</span></span>

```console
libman --help
```

<span data-ttu-id="44b7b-116">Mit dem obigen Befehl wird eine ähnliche Ausgabe wie die folgende angezeigt:</span><span class="sxs-lookup"><span data-stu-id="44b7b-116">The preceding command displays output similar to the following:</span></span>

```console
 1.0.163+g45474d37ed

Usage: libman [options] [command]

Options:
  --help|-h  Show help information
  --version  Show version information

Commands:
  cache      List or clean libman cache contents
  clean      Deletes all library files defined in libman.json from the project
  init       Create a new libman.json
  install    Add a library definition to the libman.json file, and download the 
             library to the specified location
  restore    Downloads all files from provider and saves them to specified 
             destination
  uninstall  Deletes all files for the specified library from their specified 
             destination, then removes the specified library definition from 
             libman.json
  update     Updates the specified library

Use "libman [command] --help" for more information about a command.
```

<span data-ttu-id="44b7b-117">In den folgenden Abschnitten werden die verfügbaren CLI-Befehle erläutert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-117">The following sections outline the available CLI commands.</span></span>

## <a name="initialize-libman-in-the-project"></a><span data-ttu-id="44b7b-118">Initialisieren von LibMan im Projekt</span><span class="sxs-lookup"><span data-stu-id="44b7b-118">Initialize LibMan in the project</span></span>

<span data-ttu-id="44b7b-119">Durch den Befehl `libman init` wird die Datei *libman.json* erstellt, sofern sie noch nicht existiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-119">The `libman init` command creates a *libman.json* file if one doesn't exist.</span></span> <span data-ttu-id="44b7b-120">Die erstellte Datei enthält den StandardiInhalt für Elementvorlagen.</span><span class="sxs-lookup"><span data-stu-id="44b7b-120">The file is created with the default item template content.</span></span>

### <a name="synopsis"></a><span data-ttu-id="44b7b-121">Übersicht</span><span class="sxs-lookup"><span data-stu-id="44b7b-121">Synopsis</span></span>

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a><span data-ttu-id="44b7b-122">Optionen</span><span class="sxs-lookup"><span data-stu-id="44b7b-122">Options</span></span>

<span data-ttu-id="44b7b-123">Für den Befehl `libman init` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="44b7b-123">The following options are available for the `libman init` command:</span></span>

* `-d|--default-destination <PATH>`

  <span data-ttu-id="44b7b-124">Mit dieser Option wird ein Pfad relativ zum aktuellen Ordner festgelegt.</span><span class="sxs-lookup"><span data-stu-id="44b7b-124">A path relative to the current folder.</span></span> <span data-ttu-id="44b7b-125">Bibliotheksdateien werden an diesem Ort installiert, wenn für eine Bibliothek die Eigenschaft `destination` in *libman.json* nicht definiert ist.</span><span class="sxs-lookup"><span data-stu-id="44b7b-125">Library files are installed in this location if no `destination` property is defined for a library in *libman.json*.</span></span> <span data-ttu-id="44b7b-126">Der Wert für `<PATH>` wird in die Eigenschaft `defaultDestination` in *libman.json* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="44b7b-126">The `<PATH>` value is written to the `defaultDestination` property of *libman.json*.</span></span>

* `-p|--default-provider <PROVIDER>`

  <span data-ttu-id="44b7b-127">Mit dieser Option wird der Anbieter festgelegt, der verwendet werden soll, wenn für eine Bibliothek kein Anbieter definiert wurde.</span><span class="sxs-lookup"><span data-stu-id="44b7b-127">The provider to use if no provider is defined for a given library.</span></span> <span data-ttu-id="44b7b-128">Der Wert für `<PROVIDER>` wird in die Eigenschaft `defaultProvider` in *libman.json* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="44b7b-128">The `<PROVIDER>` value is written to the `defaultProvider` property of *libman.json*.</span></span> <span data-ttu-id="44b7b-129">Ersetzen Sie `<PROVIDER>` durch einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="44b7b-129">Replace `<PROVIDER>` with one of the following values:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="44b7b-130">Beispiele</span><span class="sxs-lookup"><span data-stu-id="44b7b-130">Examples</span></span>

<span data-ttu-id="44b7b-131">Gehen Sie wie folgt vor, um die Datei *libman.json* in einem ASP.NET Core-Projekt zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="44b7b-131">To create a *libman.json* file in an ASP.NET Core project:</span></span>

* <span data-ttu-id="44b7b-132">Navigieren Sie zum Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="44b7b-132">Navigate to the project root.</span></span>
* <span data-ttu-id="44b7b-133">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="44b7b-133">Run the following command:</span></span>

  ```console
  libman init
  ```

* <span data-ttu-id="44b7b-134">Geben Sie den Namen des Standardanbieters ein, oder drücken Sie die EINGABETASTE (`Enter`), um CDNJS als Standardanbieter zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="44b7b-134">Type the name of the default provider, or press `Enter` to use the default CDNJS provider.</span></span> <span data-ttu-id="44b7b-135">Gültige Werte sind:</span><span class="sxs-lookup"><span data-stu-id="44b7b-135">Valid values include:</span></span>

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Befehl „libman init“: Standardanbieter](_static/libman-init-provider.png)

<span data-ttu-id="44b7b-137">Die Datei *libman.json* mit dem folgenden Inhalt wird dem Projektstamm hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="44b7b-137">A *libman.json* file is added to the project root with the following content:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a><span data-ttu-id="44b7b-138">Bibliotheksdateien hinzufügen</span><span class="sxs-lookup"><span data-stu-id="44b7b-138">Add library files</span></span>

<span data-ttu-id="44b7b-139">Mit dem Befehl `libman install` werden Bibliotheksdateien in das Projekt heruntergeladen und dort installiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-139">The `libman install` command downloads and installs library files into the project.</span></span> <span data-ttu-id="44b7b-140">Die Datei *libman.json* wird hinzugefügt, sofern sie noch nicht existiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-140">A *libman.json* file is added if one doesn't exist.</span></span> <span data-ttu-id="44b7b-141">Die Datei *libman.json* wird so geändert, dass dort Konfigurationsdetails für die Bibliotheksdateien gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="44b7b-141">The *libman.json* file is modified to store configuration details for the library files.</span></span>

### <a name="synopsis"></a><span data-ttu-id="44b7b-142">Übersicht</span><span class="sxs-lookup"><span data-stu-id="44b7b-142">Synopsis</span></span>

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="44b7b-143">Argumente</span><span class="sxs-lookup"><span data-stu-id="44b7b-143">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="44b7b-144">Hierbei handelt es sich um den Namen der zu installierenden Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="44b7b-144">The name of the library to install.</span></span> <span data-ttu-id="44b7b-145">Dieser Name kann eine Notation der Versionsnummer (z. B. `@1.2.0`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="44b7b-145">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="44b7b-146">Optionen</span><span class="sxs-lookup"><span data-stu-id="44b7b-146">Options</span></span>

<span data-ttu-id="44b7b-147">Für den Befehl `libman install` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="44b7b-147">The following options are available for the `libman install` command:</span></span>

* `-d|--destination <PATH>`

  <span data-ttu-id="44b7b-148">Mit dieser Option wird der Installationsort für die Bibliothek festgelegt.</span><span class="sxs-lookup"><span data-stu-id="44b7b-148">The location to install the library.</span></span> <span data-ttu-id="44b7b-149">Wenn kein Ort angegeben wird, wird der Standardspeicherort verwendet.</span><span class="sxs-lookup"><span data-stu-id="44b7b-149">If not specified, the default location is used.</span></span> <span data-ttu-id="44b7b-150">Wenn in *libman.json* kein Wert für die Eigenschaft `defaultDestination` festgelegt ist, ist diese Option erforderlich.</span><span class="sxs-lookup"><span data-stu-id="44b7b-150">If no `defaultDestination` property is specified in *libman.json*, this option is required.</span></span>

* `--files <FILE>`

  <span data-ttu-id="44b7b-151">Geben Sie den Namen der zu installierenden Datei aus der Bibliothek an.</span><span class="sxs-lookup"><span data-stu-id="44b7b-151">Specify the name of the file to install from the library.</span></span> <span data-ttu-id="44b7b-152">Wenn kein Dateiname angegeben wird, werden alle Dateien aus der Bibliothek installiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-152">If not specified, all files from the library are installed.</span></span> <span data-ttu-id="44b7b-153">Geben Sie die Option `--files` für jede der zu installierenden Dateien separat an.</span><span class="sxs-lookup"><span data-stu-id="44b7b-153">Provide one `--files` option per file to be installed.</span></span> <span data-ttu-id="44b7b-154">Relative Pfade werden ebenfalls unterstützt.</span><span class="sxs-lookup"><span data-stu-id="44b7b-154">Relative paths are supported too.</span></span> <span data-ttu-id="44b7b-155">Beispiel: `--files dist/browser/signalr.js`.</span><span class="sxs-lookup"><span data-stu-id="44b7b-155">For example: `--files dist/browser/signalr.js`.</span></span>

* `-p|--provider <PROVIDER>`

  <span data-ttu-id="44b7b-156">Mit dieser Option wird der Name des für den Erwerb der Bibliothek zu verwendenden Anbieters angegeben.</span><span class="sxs-lookup"><span data-stu-id="44b7b-156">The name of the provider to use for the library acquisition.</span></span> <span data-ttu-id="44b7b-157">Ersetzen Sie `<PROVIDER>` durch einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="44b7b-157">Replace `<PROVIDER>` with one of the following values:</span></span>
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  <span data-ttu-id="44b7b-158">Wenn kein Wert festgelegt wird, wird der Wert der Eigenschaft `defaultProvider` in *libman.json* verwendet.</span><span class="sxs-lookup"><span data-stu-id="44b7b-158">If not specified, the `defaultProvider` property in *libman.json* is used.</span></span> <span data-ttu-id="44b7b-159">Wenn in *libman.json* kein Wert für die Eigenschaft `defaultProvider` festgelegt ist, ist diese Option erforderlich.</span><span class="sxs-lookup"><span data-stu-id="44b7b-159">If no `defaultProvider` property is specified in *libman.json*, this option is required.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="44b7b-160">Beispiele</span><span class="sxs-lookup"><span data-stu-id="44b7b-160">Examples</span></span>

<span data-ttu-id="44b7b-161">Sehen Sie sich das folgende Beispiel für die Datei *libman.json* an:</span><span class="sxs-lookup"><span data-stu-id="44b7b-161">Consider the following *libman.json* file:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

<span data-ttu-id="44b7b-162">Installieren der Datei *jquery.min.js* der Version 3.2.1 von jQuery im Ordner *wwwroot/scripts/jquery* mit dem Anbieter CDNJS:</span><span class="sxs-lookup"><span data-stu-id="44b7b-162">To install the jQuery version 3.2.1 *jquery.min.js* file to the *wwwroot/scripts/jquery* folder using the CDNJS provider:</span></span>

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

<span data-ttu-id="44b7b-163">Die Datei *libman.json* ähnelt dem folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="44b7b-163">The *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    }
  ]
}
```

<span data-ttu-id="44b7b-164">Installieren der Dateien *calendar.js* und *calendar.css* aus *C:\\temp\\contosoCalendar\\* mit dem Dateisystemanbieter:</span><span class="sxs-lookup"><span data-stu-id="44b7b-164">To install the *calendar.js* and *calendar.css* files from *C:\\temp\\contosoCalendar\\* using the file system provider:</span></span>

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

<span data-ttu-id="44b7b-165">Es gibt zwei mögliche Gründe für die folgende Meldung:</span><span class="sxs-lookup"><span data-stu-id="44b7b-165">The following prompt appears for two reasons:</span></span>

* <span data-ttu-id="44b7b-166">Die Datei *libman.json* enthält keinen Wert für die Eigenschaft `defaultDestination`.</span><span class="sxs-lookup"><span data-stu-id="44b7b-166">The *libman.json* file doesn't contain a `defaultDestination` property.</span></span>
* <span data-ttu-id="44b7b-167">Die Option `-d|--destination` ist im Befehl `libman install` nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="44b7b-167">The `libman install` command doesn't contain the `-d|--destination` option.</span></span>

![Befehl „libman install“: Ziel](_static/libman-install-destination.png)

<span data-ttu-id="44b7b-169">Wenn das Standardziel bestätigt wurde, ähnelt die Datei *libman.json* dem folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="44b7b-169">After accepting the default destination, the *libman.json* file resembles the following:</span></span>

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": [
    {
      "library": "jquery@3.2.1",
      "destination": "wwwroot/scripts/jquery",
      "files": [
        "jquery.min.js"
      ]
    },
    {
      "library": "C:\\temp\\contosoCalendar\\",
      "provider": "filesystem",
      "destination": "wwwroot/lib/contosoCalendar",
      "files": [
        "calendar.js",
        "calendar.css"
      ]
    }
  ]
}
```

## <a name="restore-library-files"></a><span data-ttu-id="44b7b-170">Wiederherstellen von Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="44b7b-170">Restore library files</span></span>

<span data-ttu-id="44b7b-171">Mit dem Befehl `libman restore` werden die in *libman.json* definierten Bibliotheksdateien installiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-171">The `libman restore` command installs library files defined in *libman.json*.</span></span> <span data-ttu-id="44b7b-172">Dabei gelten folgende Regeln:</span><span class="sxs-lookup"><span data-stu-id="44b7b-172">The following rules apply:</span></span>

* <span data-ttu-id="44b7b-173">Wenn im Projektstamm keine Datei namens *libman.json* existiert, wird ein Fehler zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="44b7b-173">If no *libman.json* file exists in the project root, an error is returned.</span></span>
* <span data-ttu-id="44b7b-174">Wenn in einer Bibliothek ein Anbieter festgelegt ist, wird die Eigenschaft `defaultProvider` in *libman.json* ignoriert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-174">If a library specifies a provider, the `defaultProvider` property in *libman.json* is ignored.</span></span>
* <span data-ttu-id="44b7b-175">Wenn in einer Bibliothek ein Ziel festgelegt ist, wird die Eigenschaft `defaultDestination` in *libman.json* ignoriert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-175">If a library specifies a destination, the `defaultDestination` property in *libman.json* is ignored.</span></span>

### <a name="synopsis"></a><span data-ttu-id="44b7b-176">Übersicht</span><span class="sxs-lookup"><span data-stu-id="44b7b-176">Synopsis</span></span>

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a><span data-ttu-id="44b7b-177">Optionen</span><span class="sxs-lookup"><span data-stu-id="44b7b-177">Options</span></span>

<span data-ttu-id="44b7b-178">Für den Befehl `libman restore` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="44b7b-178">The following options are available for the `libman restore` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="44b7b-179">Beispiele</span><span class="sxs-lookup"><span data-stu-id="44b7b-179">Examples</span></span>

<span data-ttu-id="44b7b-180">Wiederherstellen der in *libman.json* definierten Bibliotheksdateien:</span><span class="sxs-lookup"><span data-stu-id="44b7b-180">To restore the library files defined in *libman.json*:</span></span>

```console
libman restore
```

## <a name="delete-library-files"></a><span data-ttu-id="44b7b-181">Löschen von Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="44b7b-181">Delete library files</span></span>

<span data-ttu-id="44b7b-182">Mit dem Befehl `libman clean` werden zuvor mit LibMan wiederhergestellte Bibliotheksdateien gelöscht.</span><span class="sxs-lookup"><span data-stu-id="44b7b-182">The `libman clean` command deletes library files previously restored via LibMan.</span></span> <span data-ttu-id="44b7b-183">Ordner, die nach diesem Vorgang leer sind, werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="44b7b-183">Folders that become empty after this operation are deleted.</span></span> <span data-ttu-id="44b7b-184">Die Konfigurationen für die Bibliotheksdateien in der Eigenschaft `libraries` in *libman.json* werden nicht entfernt.</span><span class="sxs-lookup"><span data-stu-id="44b7b-184">The library files' associated configurations in the `libraries` property of *libman.json* aren't removed.</span></span>

### <a name="synopsis"></a><span data-ttu-id="44b7b-185">Übersicht</span><span class="sxs-lookup"><span data-stu-id="44b7b-185">Synopsis</span></span>

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a><span data-ttu-id="44b7b-186">Optionen</span><span class="sxs-lookup"><span data-stu-id="44b7b-186">Options</span></span>

<span data-ttu-id="44b7b-187">Für den Befehl `libman clean` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="44b7b-187">The following options are available for the `libman clean` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="44b7b-188">Beispiele</span><span class="sxs-lookup"><span data-stu-id="44b7b-188">Examples</span></span>

<span data-ttu-id="44b7b-189">Löschen von über LibMan installierten Bibliotheksdateien:</span><span class="sxs-lookup"><span data-stu-id="44b7b-189">To delete library files installed via LibMan:</span></span>

```console
libman clean
```

## <a name="uninstall-library-files"></a><span data-ttu-id="44b7b-190">Deinstallieren von Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="44b7b-190">Uninstall library files</span></span>

<span data-ttu-id="44b7b-191">Mit dem Befehl `libman uninstall`:</span><span class="sxs-lookup"><span data-stu-id="44b7b-191">The `libman uninstall` command:</span></span>

* <span data-ttu-id="44b7b-192">werden alle der angegebenen Bibliothek zugeordneten Dateien am in *libman.json* angegebenen Ziel gelöscht.</span><span class="sxs-lookup"><span data-stu-id="44b7b-192">Deletes all files associated with the specified library from the destination in *libman.json*.</span></span>
* <span data-ttu-id="44b7b-193">wird die zugehörige Bibliothekskonfiguration in *libman.json* entfernt.</span><span class="sxs-lookup"><span data-stu-id="44b7b-193">Removes the associated library configuration from *libman.json*.</span></span>

<span data-ttu-id="44b7b-194">Ein Fehler tritt auf, wenn:</span><span class="sxs-lookup"><span data-stu-id="44b7b-194">An error occurs when:</span></span>

* <span data-ttu-id="44b7b-195">im Projektstamm keine Datei namens *libman.json* existiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-195">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="44b7b-196">die angegebene Bibliothek nicht existiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-196">The specified library doesn't exist.</span></span>

<span data-ttu-id="44b7b-197">Wenn mehr als eine Bibliothek mit demselben Namen installiert ist, werden Sie dazu aufgefordert, eine auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="44b7b-197">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="44b7b-198">Übersicht</span><span class="sxs-lookup"><span data-stu-id="44b7b-198">Synopsis</span></span>

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="44b7b-199">Argumente</span><span class="sxs-lookup"><span data-stu-id="44b7b-199">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="44b7b-200">Hierbei handelt es sich um den Namen der zu deinstallierenden Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="44b7b-200">The name of the library to uninstall.</span></span> <span data-ttu-id="44b7b-201">Dieser Name kann eine Notation der Versionsnummer (z. B. `@1.2.0`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="44b7b-201">This name may include version number notation (for example, `@1.2.0`).</span></span>

### <a name="options"></a><span data-ttu-id="44b7b-202">Optionen</span><span class="sxs-lookup"><span data-stu-id="44b7b-202">Options</span></span>

<span data-ttu-id="44b7b-203">Für den Befehl `libman uninstall` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="44b7b-203">The following options are available for the `libman uninstall` command:</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="44b7b-204">Beispiele</span><span class="sxs-lookup"><span data-stu-id="44b7b-204">Examples</span></span>

<span data-ttu-id="44b7b-205">Sehen Sie sich das folgende Beispiel für die Datei *libman.json* an:</span><span class="sxs-lookup"><span data-stu-id="44b7b-205">Consider the following *libman.json* file:</span></span>

[!code-json[](samples/LibManSample/libman.json)]

* <span data-ttu-id="44b7b-206">jQuery kann mit einem der folgenden Befehle deinstalliert werden:</span><span class="sxs-lookup"><span data-stu-id="44b7b-206">To uninstall jQuery, either of the following commands succeed:</span></span>

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* <span data-ttu-id="44b7b-207">Deinstallieren der über den Anbieter `filesystem` installierten Lodash-Dateien:</span><span class="sxs-lookup"><span data-stu-id="44b7b-207">To uninstall the Lodash files installed via the `filesystem` provider:</span></span>

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a><span data-ttu-id="44b7b-208">Aktualisieren der Bibliotheksversion</span><span class="sxs-lookup"><span data-stu-id="44b7b-208">Update library version</span></span>

<span data-ttu-id="44b7b-209">Mit dem Befehl `libman update` wird eine über LibMan installierte Bibliothek auf die angegebene Version aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-209">The `libman update` command updates a library installed via LibMan to the specified version.</span></span>

<span data-ttu-id="44b7b-210">Ein Fehler tritt auf, wenn:</span><span class="sxs-lookup"><span data-stu-id="44b7b-210">An error occurs when:</span></span>

* <span data-ttu-id="44b7b-211">im Projektstamm keine Datei namens *libman.json* existiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-211">No *libman.json* file exists in the project root.</span></span>
* <span data-ttu-id="44b7b-212">die angegebene Bibliothek nicht existiert.</span><span class="sxs-lookup"><span data-stu-id="44b7b-212">The specified library doesn't exist.</span></span>

<span data-ttu-id="44b7b-213">Wenn mehr als eine Bibliothek mit demselben Namen installiert ist, werden Sie dazu aufgefordert, eine auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="44b7b-213">If more than one library with the same name is installed, you're prompted to choose one.</span></span>

### <a name="synopsis"></a><span data-ttu-id="44b7b-214">Übersicht</span><span class="sxs-lookup"><span data-stu-id="44b7b-214">Synopsis</span></span>

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="44b7b-215">Argumente</span><span class="sxs-lookup"><span data-stu-id="44b7b-215">Arguments</span></span>

`LIBRARY`

<span data-ttu-id="44b7b-216">Hierbei handelt es sich um den Namen der zu aktualisierenden Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="44b7b-216">The name of the library to update.</span></span>

### <a name="options"></a><span data-ttu-id="44b7b-217">Optionen</span><span class="sxs-lookup"><span data-stu-id="44b7b-217">Options</span></span>

<span data-ttu-id="44b7b-218">Für den Befehl `libman update` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="44b7b-218">The following options are available for the `libman update` command:</span></span>

* `-pre`

  <span data-ttu-id="44b7b-219">Abrufen der aktuellen Vorabversion der Bibliothek</span><span class="sxs-lookup"><span data-stu-id="44b7b-219">Obtain the latest prerelease version of the library.</span></span>

* `--to <VERSION>`

  <span data-ttu-id="44b7b-220">Abrufen einer bestimmten Version der Bibliothek</span><span class="sxs-lookup"><span data-stu-id="44b7b-220">Obtain a specific version of the library.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="44b7b-221">Beispiele</span><span class="sxs-lookup"><span data-stu-id="44b7b-221">Examples</span></span>

* <span data-ttu-id="44b7b-222">Aktualisieren von jQuery auf die aktuelle Version:</span><span class="sxs-lookup"><span data-stu-id="44b7b-222">To update jQuery to the latest version:</span></span>

  ```console
  libman update jquery
  ```

* <span data-ttu-id="44b7b-223">Aktualisieren von jQuery auf Version 3.3.1:</span><span class="sxs-lookup"><span data-stu-id="44b7b-223">To update jQuery to version 3.3.1:</span></span>

  ```console
  libman update jquery --to 3.3.1
  ```

* <span data-ttu-id="44b7b-224">Aktualisieren von jQuery auf die aktuelle Vorabversion:</span><span class="sxs-lookup"><span data-stu-id="44b7b-224">To update jQuery to the latest prerelease version:</span></span>

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a><span data-ttu-id="44b7b-225">Bibliothekscache verwalten</span><span class="sxs-lookup"><span data-stu-id="44b7b-225">Manage library cache</span></span>

<span data-ttu-id="44b7b-226">Mit dem Befehl `libman cache` wird der LibMan-Bibliothekscache verwaltet.</span><span class="sxs-lookup"><span data-stu-id="44b7b-226">The `libman cache` command manages the LibMan library cache.</span></span> <span data-ttu-id="44b7b-227">Der Anbieter `filesystem` verwendet den Bibliothekscache nicht.</span><span class="sxs-lookup"><span data-stu-id="44b7b-227">The `filesystem` provider doesn't use the library cache.</span></span>

### <a name="synopsis"></a><span data-ttu-id="44b7b-228">Übersicht</span><span class="sxs-lookup"><span data-stu-id="44b7b-228">Synopsis</span></span>

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a><span data-ttu-id="44b7b-229">Argumente</span><span class="sxs-lookup"><span data-stu-id="44b7b-229">Arguments</span></span>

`PROVIDER`

<span data-ttu-id="44b7b-230">Dieses Argument wird nur mit dem Befehl `clean` verwendet.</span><span class="sxs-lookup"><span data-stu-id="44b7b-230">Only used with the `clean` command.</span></span> <span data-ttu-id="44b7b-231">Damit wird der zu bereinigende Anbietercache angegeben.</span><span class="sxs-lookup"><span data-stu-id="44b7b-231">Specifies the provider cache to clean.</span></span> <span data-ttu-id="44b7b-232">Gültige Werte sind:</span><span class="sxs-lookup"><span data-stu-id="44b7b-232">Valid values include:</span></span>

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a><span data-ttu-id="44b7b-233">Optionen</span><span class="sxs-lookup"><span data-stu-id="44b7b-233">Options</span></span>

<span data-ttu-id="44b7b-234">Für den Befehl `libman cache` sind die folgenden Optionen verfügbar:</span><span class="sxs-lookup"><span data-stu-id="44b7b-234">The following options are available for the `libman cache` command:</span></span>

* `--files`

  <span data-ttu-id="44b7b-235">Auflisten der zwischengespeicherten Dateien</span><span class="sxs-lookup"><span data-stu-id="44b7b-235">List the names of files that are cached.</span></span>

* `--libraries`

  <span data-ttu-id="44b7b-236">Auflisten der zwischengespeicherten Bibliotheken</span><span class="sxs-lookup"><span data-stu-id="44b7b-236">List the names of libraries that are cached.</span></span>

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a><span data-ttu-id="44b7b-237">Beispiele</span><span class="sxs-lookup"><span data-stu-id="44b7b-237">Examples</span></span>

* <span data-ttu-id="44b7b-238">Verwenden Sie einen der folgenden Befehle, um die Namen der zwischengespeicherten Bibliotheken nach Anbietern sortiert anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="44b7b-238">To view the names of cached libraries per provider, use one of the following commands:</span></span>

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  <span data-ttu-id="44b7b-239">Dadurch werden Informationen angezeigt, die mit denen der folgenden Ausgabe vergleichbar sind:</span><span class="sxs-lookup"><span data-stu-id="44b7b-239">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      font-awesome
      jquery
      knockout
      lodash.js
      react
  ```

* <span data-ttu-id="44b7b-240">Anzeigen der Namen der zwischengespeicherten Bibliotheksdateien nach Anbietern sortiert:</span><span class="sxs-lookup"><span data-stu-id="44b7b-240">To view the names of cached library files per provider:</span></span>

  ```console
  libman cache list --files
  ```

  <span data-ttu-id="44b7b-241">Dadurch werden Informationen angezeigt, die mit denen der folgenden Ausgabe vergleichbar sind:</span><span class="sxs-lookup"><span data-stu-id="44b7b-241">Output similar to the following is displayed:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout:
          <list omitted for brevity>
      react:
          <list omitted for brevity>
      vue:
          <list omitted for brevity>
  cdnjs:
      font-awesome
          metadata.json
      jquery
          metadata.json
          3.2.1\core.js
          3.2.1\jquery.js
          3.2.1\jquery.min.js
          3.2.1\jquery.min.map
          3.2.1\jquery.slim.js
          3.2.1\jquery.slim.min.js
          3.2.1\jquery.slim.min.map
          3.3.1\core.js
          3.3.1\jquery.js
          3.3.1\jquery.min.js
          3.3.1\jquery.min.map
          3.3.1\jquery.slim.js
          3.3.1\jquery.slim.min.js
          3.3.1\jquery.slim.min.map
      knockout
          metadata.json
          3.4.2\knockout-debug.js
          3.4.2\knockout-min.js
      lodash.js
          metadata.json
          4.17.10\lodash.js
          4.17.10\lodash.min.js
      react
          metadata.json
  ```

  <span data-ttu-id="44b7b-242">Beachten Sie, dass bei der obigen Ausgabe die Versionen 3.2.1 und 3.3.1 von jQuery unter dem Anbieter CDNJS zwischengespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="44b7b-242">Notice the preceding output shows that jQuery versions 3.2.1 and 3.3.1 are cached under the CDNJS provider.</span></span>

* <span data-ttu-id="44b7b-243">Leeren des Bibliothekscaches für den Anbieter CDNJS:</span><span class="sxs-lookup"><span data-stu-id="44b7b-243">To empty the library cache for the CDNJS provider:</span></span>

  ```console
  libman cache clean cdnjs
  ```

  <span data-ttu-id="44b7b-244">Wenn der Cache für den Anbieter CDNJS geleert wurde, wird nach dem Ausführen des Befehls `libman cache list` Folgendes angezeigt:</span><span class="sxs-lookup"><span data-stu-id="44b7b-244">After emptying the CDNJS provider cache, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      knockout
      react
      vue
  cdnjs:
      (empty)
  ```

* <span data-ttu-id="44b7b-245">Leeren des Caches für alle unterstützten Anbieter:</span><span class="sxs-lookup"><span data-stu-id="44b7b-245">To empty the cache for all supported providers:</span></span>

  ```console
  libman cache clean
  ```

  <span data-ttu-id="44b7b-246">Wenn alle Anbietercaches geleert wurden, wird nach dem Ausführen des Befehls `libman cache list` Folgendes angezeigt:</span><span class="sxs-lookup"><span data-stu-id="44b7b-246">After emptying all provider caches, the `libman cache list` command displays the following:</span></span>

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a><span data-ttu-id="44b7b-247">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="44b7b-247">Additional resources</span></span>

* [<span data-ttu-id="44b7b-248">Installieren eines globalen Tools</span><span class="sxs-lookup"><span data-stu-id="44b7b-248">Install a Global Tool</span></span>](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [<span data-ttu-id="44b7b-249">GitHub-Repository für LibMan</span><span class="sxs-lookup"><span data-stu-id="44b7b-249">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
