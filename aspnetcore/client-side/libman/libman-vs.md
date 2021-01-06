---
title: Verwenden von LibMan mit ASP.NET Core in Visual Studio
author: scottaddie
description: Lernen Sie, wie Sie LibMan in einem ASP.NET Core-Projekt mit Visual Studio verwenden können.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
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
uid: client-side/libman/libman-vs
ms.openlocfilehash: 1c97f5d7fbf64c5043e6d2277091b9a477833bf1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054709"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a><span data-ttu-id="872f6-103">Verwenden von LibMan mit ASP.NET Core in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="872f6-103">Use LibMan with ASP.NET Core in Visual Studio</span></span>

<span data-ttu-id="872f6-104">Von [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="872f6-104">By [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="872f6-105">Visual Studio verfügt über die integrierte Unterstützung für [LibMan](xref:client-side/libman/index) in ASP.NET Core-Projekten, einschließlich:</span><span class="sxs-lookup"><span data-stu-id="872f6-105">Visual Studio has built-in support for [LibMan](xref:client-side/libman/index) in ASP.NET Core projects, including:</span></span>

* <span data-ttu-id="872f6-106">Unterstützung für die Konfiguration und Ausführung von LibMan-Wiederherstellungvorgängen für den Build.</span><span class="sxs-lookup"><span data-stu-id="872f6-106">Support for configuring and running LibMan restore operations on build.</span></span>
* <span data-ttu-id="872f6-107">Menüelemente zum Auslösen von LibMan-Wiederherstellungs- und -Bereinigungsvorgängen.</span><span class="sxs-lookup"><span data-stu-id="872f6-107">Menu items for triggering LibMan restore and clean operations.</span></span>
* <span data-ttu-id="872f6-108">Dialogfeld für die Suche nach Bibliotheken und zum Hinzufügen der Dateien zu einem Projekt.</span><span class="sxs-lookup"><span data-stu-id="872f6-108">Search dialog for finding libraries and adding the files to a project.</span></span>
* <span data-ttu-id="872f6-109">Unterstützung der Bearbeitung für *libman.json*&mdash;die LibMan-Manifestdatei.</span><span class="sxs-lookup"><span data-stu-id="872f6-109">Editing support for *libman.json*&mdash;the LibMan manifest file.</span></span>

<span data-ttu-id="872f6-110">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="872f6-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="872f6-111">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="872f6-111">Prerequisites</span></span>

* <span data-ttu-id="872f6-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="872f6-112">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>

## <a name="add-library-files"></a><span data-ttu-id="872f6-113">Hinzufügen von Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="872f6-113">Add library files</span></span>

<span data-ttu-id="872f6-114">Bibliotheksdateien können einem ASP.NET Core-Projekt auf zwei verschiedene Arten hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="872f6-114">Library files can be added to an ASP.NET Core project in two different ways:</span></span>

1. [<span data-ttu-id="872f6-115">Verwenden des Dialogfelds „Clientseitige Bibliothek hinzufügen“</span><span class="sxs-lookup"><span data-stu-id="872f6-115">Use the Add Client-Side Library dialog</span></span>](#use-the-add-client-side-library-dialog)
1. [<span data-ttu-id="872f6-116">Manuelles Konfigurieren von LibMan-Manifestdateieinträgen</span><span class="sxs-lookup"><span data-stu-id="872f6-116">Manually configure LibMan manifest file entries</span></span>](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a><span data-ttu-id="872f6-117">Verwenden des Dialogfelds „Clientseitige Bibliothek hinzufügen“</span><span class="sxs-lookup"><span data-stu-id="872f6-117">Use the Add Client-Side Library dialog</span></span>

<span data-ttu-id="872f6-118">Befolgen Sie diese Schritte, um eine clientseitige Bibliothek zu installieren:</span><span class="sxs-lookup"><span data-stu-id="872f6-118">Follow these steps to install a client-side library:</span></span>

* <span data-ttu-id="872f6-119">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektordner, in dem die Dateien hinzugefügt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="872f6-119">In **Solution Explorer**, right-click the project folder in which the files should be added.</span></span> <span data-ttu-id="872f6-120">Wählen Sie **Hinzufügen** > **Clientseitige Bibliothek** aus.</span><span class="sxs-lookup"><span data-stu-id="872f6-120">Choose **Add** > **Client-Side Library**.</span></span> <span data-ttu-id="872f6-121">Das Dialogfeld **Clientseitige Bibliothek hinzufügen** wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="872f6-121">The **Add Client-Side Library** dialog appears:</span></span>

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“](_static/add-library-dialog.png)

* <span data-ttu-id="872f6-123">Wählen Sie den Bibliotheksanbieter aus der Dropdownliste **Anbieter** aus.</span><span class="sxs-lookup"><span data-stu-id="872f6-123">Select the library provider from the **Provider** drop down.</span></span> <span data-ttu-id="872f6-124">CDNJS ist der Standardanbieter.</span><span class="sxs-lookup"><span data-stu-id="872f6-124">CDNJS is the default provider.</span></span>
* <span data-ttu-id="872f6-125">Geben Sie den Namen der abzurufenden Bibliothek in das Textfeld **Bibliothek** ein.</span><span class="sxs-lookup"><span data-stu-id="872f6-125">Type the library name to fetch in the **Library** text box.</span></span> <span data-ttu-id="872f6-126">IntelliSense stellt eine Liste von Bibliotheken zur Verfügung, die mit dem bereitgestellten Text beginnt.</span><span class="sxs-lookup"><span data-stu-id="872f6-126">IntelliSense provides a list of libraries beginning with the provided text.</span></span>
* <span data-ttu-id="872f6-127">Wählen Sie die Bibliothek aus der IntelliSense-Liste aus.</span><span class="sxs-lookup"><span data-stu-id="872f6-127">Select the library from the IntelliSense list.</span></span> <span data-ttu-id="872f6-128">Beachten Sie, dass dem Bibliotheksnamen das Symbol `@` und die letzte stabile Version, die dem ausgewählten Anbieter bekannt ist, angefügt ist.</span><span class="sxs-lookup"><span data-stu-id="872f6-128">Notice the library name is suffixed with the `@` symbol and the latest stable version known to the selected provider.</span></span>
* <span data-ttu-id="872f6-129">Entscheiden Sie, welche Dateien einbezogen werden sollen:</span><span class="sxs-lookup"><span data-stu-id="872f6-129">Decide which files to include:</span></span>
  * <span data-ttu-id="872f6-130">Aktivieren Sie das Optionsfeld **Alle Bibliotheksdateien einbeziehen**, um alle Dateien der Bibliothek einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="872f6-130">Select the **Include all library files** radio button to include all of the library's files.</span></span>
  * <span data-ttu-id="872f6-131">Aktivieren Sie das Optionsfeld **Bestimmte Dateien auswählen**, um eine Teilmenge der Dateien der Bibliothek einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="872f6-131">Select the **Choose specific files** radio button to include a subset of the library's files.</span></span> <span data-ttu-id="872f6-132">Wenn das Optionsfeld aktiviert ist, wird die Dateiauswahlstruktur aktiviert.</span><span class="sxs-lookup"><span data-stu-id="872f6-132">When the radio button is selected, the file selector tree is enabled.</span></span> <span data-ttu-id="872f6-133">Aktivieren Sie die Kästchen links neben den Dateinamen, die heruntergeladen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="872f6-133">Check the boxes to the left of the file names to download.</span></span>
* <span data-ttu-id="872f6-134">Geben Sie den Projektordner für die Speicherung der Dateien im Textfeld **Zielspeicherort** an.</span><span class="sxs-lookup"><span data-stu-id="872f6-134">Specify the project folder for storing the files in the **Target Location** text box.</span></span> <span data-ttu-id="872f6-135">Als Empfehlung sollten Sie jede Bibliothek in einem separaten Ordner speichern.</span><span class="sxs-lookup"><span data-stu-id="872f6-135">As a recommendation, store each library in a separate folder.</span></span>

  <span data-ttu-id="872f6-136">Der vorgeschlagene Ordner **Zielspeicherort** basiert auf dem Speicherort, von dem aus das Dialogfeld gestartet wurde:</span><span class="sxs-lookup"><span data-stu-id="872f6-136">The suggested **Target Location** folder is based on the location from which the dialog launched:</span></span>

  * <span data-ttu-id="872f6-137">Beim Starten vom Projektstamm aus:</span><span class="sxs-lookup"><span data-stu-id="872f6-137">If launched from the project root:</span></span>
    * <span data-ttu-id="872f6-138">*wwwroot/lib* wird verwendet, wenn *wwwroot* vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="872f6-138">*wwwroot/lib* is used if *wwwroot* exists.</span></span>
    * <span data-ttu-id="872f6-139">*lib* wird verwendet, wenn *wwwroot* nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="872f6-139">*lib* is used if *wwwroot* doesn't exist.</span></span>
  * <span data-ttu-id="872f6-140">Beim Starten von einem Projektordner aus, wird der entsprechende Ordnername verwendet.</span><span class="sxs-lookup"><span data-stu-id="872f6-140">If launched from a project folder, the corresponding folder name is used.</span></span>

  <span data-ttu-id="872f6-141">Dem Ordnervorschlag wird der Name der Bibliothek angefügt.</span><span class="sxs-lookup"><span data-stu-id="872f6-141">The folder suggestion is suffixed with the library name.</span></span> <span data-ttu-id="872f6-142">In der folgenden Tabelle werden die Ordnervorschläge für die Installation von jQuery in einem Razor Pages-Projekt dargestellt.</span><span class="sxs-lookup"><span data-stu-id="872f6-142">The following table illustrates folder suggestions when installing jQuery in a Razor Pages project.</span></span>
  
  |<span data-ttu-id="872f6-143">Startposition</span><span class="sxs-lookup"><span data-stu-id="872f6-143">Launch location</span></span>                           |<span data-ttu-id="872f6-144">Vorgeschlagener Ordner</span><span class="sxs-lookup"><span data-stu-id="872f6-144">Suggested folder</span></span>      |
  |------------------------------------------|----------------------|
  |<span data-ttu-id="872f6-145">Projektstamm (wenn *wwwroot* vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="872f6-145">project root (if *wwwroot* exists)</span></span>        |<span data-ttu-id="872f6-146">*wwwroot/lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="872f6-146">*wwwroot/lib/jquery/*</span></span> |
  |<span data-ttu-id="872f6-147">Projektstamm (wenn *wwwroot* nicht vorhanden ist)</span><span class="sxs-lookup"><span data-stu-id="872f6-147">project root (if *wwwroot* doesn't exist)</span></span> |<span data-ttu-id="872f6-148">*lib/jquery/*</span><span class="sxs-lookup"><span data-stu-id="872f6-148">*lib/jquery/*</span></span>         |
  |<span data-ttu-id="872f6-149">*Pages*-Ordner im Projekt</span><span class="sxs-lookup"><span data-stu-id="872f6-149">*Pages* folder in project</span></span>                 |<span data-ttu-id="872f6-150">*Pages/jquery/*</span><span class="sxs-lookup"><span data-stu-id="872f6-150">*Pages/jquery/*</span></span>       |

* <span data-ttu-id="872f6-151">Klicken Sie auf die Schaltfläche **Installieren**, um die Dateien gemäß der Konfiguration in *libman.json* herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="872f6-151">Click the **Install** button to download the files, per the configuration in *libman.json*.</span></span>
* <span data-ttu-id="872f6-152">Überprüfen Sie den **Bibliotheks-Manager**-Feed des Fensters **Ausgabe** auf Installationsdetails.</span><span class="sxs-lookup"><span data-stu-id="872f6-152">Review the **Library Manager** feed of the **Output** window for installation details.</span></span> <span data-ttu-id="872f6-153">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="872f6-153">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a><span data-ttu-id="872f6-154">Manuelles Konfigurieren von LibMan-Manifestdateieinträgen</span><span class="sxs-lookup"><span data-stu-id="872f6-154">Manually configure LibMan manifest file entries</span></span>

<span data-ttu-id="872f6-155">Alle LibMan-Vorgänge in Visual Studio basieren auf dem Inhalt des LibMan-Manifests des Projektstamms (*libman.json*).</span><span class="sxs-lookup"><span data-stu-id="872f6-155">All LibMan operations in Visual Studio are based on the content of the project root's LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="872f6-156">Sie können *libman.json* manuell bearbeiten, um Bibliotheksdateien für das Projekt zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="872f6-156">You can manually edit *libman.json* to configure library files for the project.</span></span> <span data-ttu-id="872f6-157">Visual Studio stellt alle Bibliotheksdateien wieder her, sobald *libman.json* gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="872f6-157">Visual Studio restores all library files once *libman.json* is saved.</span></span>

<span data-ttu-id="872f6-158">Es gibt folgende Möglichkeiten, *libman.json* zur Bearbeitung zu öffnen:</span><span class="sxs-lookup"><span data-stu-id="872f6-158">To open *libman.json* for editing, the following options exist:</span></span>

* <span data-ttu-id="872f6-159">Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="872f6-159">Double-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="872f6-160">Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Clientseitige Bibliotheken verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="872f6-160">Right-click the project in **Solution Explorer** and select **Manage Client-Side Libraries**.</span></span> <span data-ttu-id="872f6-161">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="872f6-161">**&#8224;**</span></span>
* <span data-ttu-id="872f6-162">Wählen Sie **Clientseitige Bibliotheken verwalten** im Visual Studio-Menü **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="872f6-162">Select **Manage Client-Side Libraries** from the Visual Studio **Project** menu.</span></span> <span data-ttu-id="872f6-163">**&#8224;**</span><span class="sxs-lookup"><span data-stu-id="872f6-163">**&#8224;**</span></span>

<span data-ttu-id="872f6-164">**&#8224;** Wenn die Datei *libman.json* nicht bereits im Projektstamm vorhanden ist, wird sie mit dem Inhalt der Standardelementvorlage erstellt.</span><span class="sxs-lookup"><span data-stu-id="872f6-164">**&#8224;** If the *libman.json* file doesn't already exist in the project root, it will be created with the default item template content.</span></span>

<span data-ttu-id="872f6-165">Visual Studio bietet umfangreiche Unterstützung für die JSON-Bearbeitung wie die farbliche Kennzeichnung, Formatierung, IntelliSense und Schemaüberprüfung.</span><span class="sxs-lookup"><span data-stu-id="872f6-165">Visual Studio offers rich JSON editing support such as colorization, formatting, IntelliSense, and schema validation.</span></span> <span data-ttu-id="872f6-166">Das JSON-Schema des LibMan-Manifests befindet sich unter [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span><span class="sxs-lookup"><span data-stu-id="872f6-166">The LibMan manifest's JSON schema is found at [https://json.schemastore.org/libman](https://json.schemastore.org/libman).</span></span>

<span data-ttu-id="872f6-167">Mit der folgenden Manifestdatei ruft LibMan Dateien für die in der `libraries`-Eigenschaft definierte Konfiguration ab.</span><span class="sxs-lookup"><span data-stu-id="872f6-167">With the following manifest file, LibMan retrieves files per the configuration defined in the `libraries` property.</span></span> <span data-ttu-id="872f6-168">Es folgt eine Erläuterung der in `libraries` definierten Objektliterale:</span><span class="sxs-lookup"><span data-stu-id="872f6-168">An explanation of the object literals defined within `libraries` follows:</span></span>

* <span data-ttu-id="872f6-169">Eine Teilmenge von [jQuery](https://jquery.com/) Version 3.3.1 wird vom CDNJS-Anbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="872f6-169">A subset of [jQuery](https://jquery.com/) version 3.3.1 is retrieved from the CDNJS provider.</span></span> <span data-ttu-id="872f6-170">Die Teilmenge ist in der `files`-Eigenschaft&mdash;*jquery.min.js*, *jquery.js* und *jquery.min.map* definiert.</span><span class="sxs-lookup"><span data-stu-id="872f6-170">The subset is defined in the `files` property&mdash;*jquery.min.js*, *jquery.js*, and *jquery.min.map*.</span></span> <span data-ttu-id="872f6-171">Die Dateien werden im Ordner *wwwroot/lib/jquery* des Projekts abgelegt.</span><span class="sxs-lookup"><span data-stu-id="872f6-171">The files are placed in the project's *wwwroot/lib/jquery* folder.</span></span>
* <span data-ttu-id="872f6-172">[Bootstrap](https://getbootstrap.com/) Version 4.1.3 wird vollständig abgerufen und im Ordner *wwwroot/lib/bootstrap* abgelegt.</span><span class="sxs-lookup"><span data-stu-id="872f6-172">The entirety of [Bootstrap](https://getbootstrap.com/) version 4.1.3 is retrieved and placed in a *wwwroot/lib/bootstrap* folder.</span></span> <span data-ttu-id="872f6-173">Die Eigenschaft `provider` des Objektliterals setzt den Eigenschaftswert `defaultProvider` außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="872f6-173">The object literal's `provider` property overrides the `defaultProvider` property value.</span></span> <span data-ttu-id="872f6-174">LibMan ruft die Bootstrap-Dateien vom unpkg-Anbieter ab.</span><span class="sxs-lookup"><span data-stu-id="872f6-174">LibMan retrieves the Bootstrap files from the unpkg provider.</span></span>
* <span data-ttu-id="872f6-175">Eine Teilmenge von [Lodash](https://lodash.com/) wurde von einer leitenden Instanz innerhalb der Organisation genehmigt.</span><span class="sxs-lookup"><span data-stu-id="872f6-175">A subset of [Lodash](https://lodash.com/) was approved by a governing body within the organization.</span></span> <span data-ttu-id="872f6-176">Die Dateien *lodash.js* und *lodash.min.js* werden aus dem lokalen Dateisystem unter *C:\\temp\\lodash\\* abgerufen.</span><span class="sxs-lookup"><span data-stu-id="872f6-176">The *lodash.js* and *lodash.min.js* files are retrieved from the local file system at *C:\\temp\\lodash\\*.</span></span> <span data-ttu-id="872f6-177">Die Dateien werden in den Ordner *wwwroot/lib/lodash* des Projekts kopiert.</span><span class="sxs-lookup"><span data-stu-id="872f6-177">The files are copied to the project's *wwwroot/lib/lodash* folder.</span></span>

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> <span data-ttu-id="872f6-178">LibMan unterstützt nur eine Version jeder Bibliothek von den einzelnen Anbietern.</span><span class="sxs-lookup"><span data-stu-id="872f6-178">LibMan only supports one version of each library from each provider.</span></span> <span data-ttu-id="872f6-179">Für die Datei *libman.json* tritt bei der Schemaüberprüfung ein Fehler auf, wenn sie zwei Bibliotheken mit demselben Bibliotheksnamen für einen bestimmten Anbieter enthält.</span><span class="sxs-lookup"><span data-stu-id="872f6-179">The *libman.json* file fails schema validation if it contains two libraries with the same library name for a given provider.</span></span>

## <a name="restore-library-files"></a><span data-ttu-id="872f6-180">Wiederherstellen von Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="872f6-180">Restore library files</span></span>

<span data-ttu-id="872f6-181">Zum Wiederherstellen von Bibliotheksdateien in Visual Studio muss eine gültige *libman.json*-Datei im Projektstamm vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="872f6-181">To restore library files from within Visual Studio, there must be a valid *libman.json* file in the project root.</span></span> <span data-ttu-id="872f6-182">Wiederhergestellte Dateien werden im Projekt an dem für jede Bibliothek angegebenen Ort platziert.</span><span class="sxs-lookup"><span data-stu-id="872f6-182">Restored files are placed in the project at the location specified for each library.</span></span>

<span data-ttu-id="872f6-183">Bibliotheksdateien können in einem ASP.NET Core-Projekt auf zwei Arten wiederhergestellt werden:</span><span class="sxs-lookup"><span data-stu-id="872f6-183">Library files can be restored in an ASP.NET Core project in two ways:</span></span>

1. [<span data-ttu-id="872f6-184">Wiederherstellen von Dateien während der Erstellung</span><span class="sxs-lookup"><span data-stu-id="872f6-184">Restore files during build</span></span>](#restore-files-during-build)
1. [<span data-ttu-id="872f6-185">Manuelles Wiederherstellen von Dateien</span><span class="sxs-lookup"><span data-stu-id="872f6-185">Restore files manually</span></span>](#restore-files-manually)

### <a name="restore-files-during-build"></a><span data-ttu-id="872f6-186">Wiederherstellen von Dateien während der Erstellung</span><span class="sxs-lookup"><span data-stu-id="872f6-186">Restore files during build</span></span>

<span data-ttu-id="872f6-187">LibMan kann die definierten Bibliotheksdateien im Rahmen des Buildprozesses wiederherstellen.</span><span class="sxs-lookup"><span data-stu-id="872f6-187">LibMan can restore the defined library files as part of the build process.</span></span> <span data-ttu-id="872f6-188">Standardmäßig ist die *Wiederherstellung während des Builds* deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="872f6-188">By default, the *restore-on-build* behavior is disabled.</span></span>

<span data-ttu-id="872f6-189">So aktivieren und testen Sie das Verhalten „Wiederherstellung während des Builds“</span><span class="sxs-lookup"><span data-stu-id="872f6-189">To enable and test the restore-on-build behavior:</span></span>

* <span data-ttu-id="872f6-190">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf *libman.json*, und wählen Sie im Kontextmenü **Wiederherstellung clientseitiger Bibliotheken bei Builderstellung aktivieren** aus.</span><span class="sxs-lookup"><span data-stu-id="872f6-190">Right-click *libman.json* in **Solution Explorer** and select **Enable Restore Client-Side Libraries on Build** from the context menu.</span></span>
* <span data-ttu-id="872f6-191">Klicken Sie auf die Schaltfläche **Ja**, wenn Sie aufgefordert werden, ein NuGet-Paket zu installieren.</span><span class="sxs-lookup"><span data-stu-id="872f6-191">Click the **Yes** button when prompted to install a NuGet package.</span></span> <span data-ttu-id="872f6-192">Das NuGet-Paket [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) wird dem Projekt hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="872f6-192">The [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet package is added to the project:</span></span>

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* <span data-ttu-id="872f6-193">Erstellen Sie das Projekt, um die Wiederherstellung der LibMan-Datei zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="872f6-193">Build the project to confirm LibMan file restoration occurs.</span></span> <span data-ttu-id="872f6-194">Das `Microsoft.Web.LibraryManager.Build`-Paket fügt ein MSBuild-Target ein, das LibMan während des Buildvorgangs des Projekts ausführt.</span><span class="sxs-lookup"><span data-stu-id="872f6-194">The `Microsoft.Web.LibraryManager.Build` package injects an MSBuild target that runs LibMan during the project's build operation.</span></span>
* <span data-ttu-id="872f6-195">Überprüfen Sie den **Build**-Feed des Fensters **Ausgabe** auf ein LibMan-Aktivitätsprotokoll:</span><span class="sxs-lookup"><span data-stu-id="872f6-195">Review the **Build** feed of the **Output** window for a LibMan activity log:</span></span>

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

<span data-ttu-id="872f6-196">Wenn das Verhalten „Wiederherstellung während des Builds“ aktiviert ist, zeigt das Kontextmenü *libman.json* die Option **Wiederherstellung clientseitiger Bibliotheken bei Builderstellung deaktivieren** an.</span><span class="sxs-lookup"><span data-stu-id="872f6-196">When the restore-on-build behavior is enabled, the *libman.json* context menu displays a **Disable Restore Client-Side Libraries on Build** option.</span></span> <span data-ttu-id="872f6-197">Durch Auswahl dieser Option wird der Paketverweis `Microsoft.Web.LibraryManager.Build` aus der Projektdatei entfernt.</span><span class="sxs-lookup"><span data-stu-id="872f6-197">Selecting this option removes the `Microsoft.Web.LibraryManager.Build` package reference from the project file.</span></span> <span data-ttu-id="872f6-198">Folglich werden die clientseitigen Bibliotheken nicht mehr bei jedem Build wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="872f6-198">Consequently, the client-side libraries are no longer restored on each build.</span></span>

<span data-ttu-id="872f6-199">Unabhängig von der Einstellung „Wiederherstellung während des Builds“ können Sie die Wiederherstellung jederzeit manuell über das Kontextmenü *libman.json* durchführen.</span><span class="sxs-lookup"><span data-stu-id="872f6-199">Regardless of the restore-on-build setting, you can manually restore at any time from the *libman.json* context menu.</span></span> <span data-ttu-id="872f6-200">Weitere Informationen finden Sie unter [Manuelles Wiederherstellen von Dateien](#restore-files-manually).</span><span class="sxs-lookup"><span data-stu-id="872f6-200">For more information, see [Restore files manually](#restore-files-manually).</span></span>

### <a name="restore-files-manually"></a><span data-ttu-id="872f6-201">Manuelles Wiederherstellen von Dateien</span><span class="sxs-lookup"><span data-stu-id="872f6-201">Restore files manually</span></span>

<span data-ttu-id="872f6-202">So stellen Sie Bibliotheksdateien manuell wieder her</span><span class="sxs-lookup"><span data-stu-id="872f6-202">To manually restore library files:</span></span>

* <span data-ttu-id="872f6-203">Gehen Sie für alle Projekte in der Lösung wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="872f6-203">For all projects in the solution:</span></span>
  * <span data-ttu-id="872f6-204">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Namen der Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="872f6-204">Right-click the solution name in **Solution Explorer**.</span></span>
  * <span data-ttu-id="872f6-205">Wählen Sie die Option **Wiederherstellung clientseitiger Bibliotheken bei Builderstellung aktivieren** aus.</span><span class="sxs-lookup"><span data-stu-id="872f6-205">Select the **Restore Client-Side Libraries** option.</span></span>
* <span data-ttu-id="872f6-206">Gehen Sie für ein bestimmtes Projekt wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="872f6-206">For a specific project:</span></span>
  * <span data-ttu-id="872f6-207">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Datei *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="872f6-207">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
  * <span data-ttu-id="872f6-208">Wählen Sie die Option **Wiederherstellung clientseitiger Bibliotheken bei Builderstellung aktivieren** aus.</span><span class="sxs-lookup"><span data-stu-id="872f6-208">Select the **Restore Client-Side Libraries** option.</span></span>

<span data-ttu-id="872f6-209">Gehen Sie wie folgt vor, während der Wiederherstellungsvorgang ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="872f6-209">While the restore operation is running:</span></span>

* <span data-ttu-id="872f6-210">Das Symbol für das Aufgabenstatuscenter in der Statusleiste von Visual Studio wird animiert und zeigt *Wiederherstellungsvorgang gestartet* an.</span><span class="sxs-lookup"><span data-stu-id="872f6-210">The Task Status Center (TSC) icon on the Visual Studio status bar will be animated and will read *Restore operation started*.</span></span> <span data-ttu-id="872f6-211">Wenn Sie auf das Symbol klicken, wird eine QuickInfo mit den bekannten Hintergrundaufgaben geöffnet.</span><span class="sxs-lookup"><span data-stu-id="872f6-211">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="872f6-212">Die Nachrichten werden an die Statusleiste und den **Bibliotheks-Manager**-Feed des Fensters **Ausgabe** gesendet.</span><span class="sxs-lookup"><span data-stu-id="872f6-212">Messages will be sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="872f6-213">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="872f6-213">For example:</span></span>

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a><span data-ttu-id="872f6-214">Löschen von Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="872f6-214">Delete library files</span></span>

<span data-ttu-id="872f6-215">So führen Sie den *Bereinigungsvorgang* aus, der zuvor in Visual Studio wiederhergestellte Bibliotheksdateien löscht</span><span class="sxs-lookup"><span data-stu-id="872f6-215">To perform the *clean* operation, which deletes library files previously restored in Visual Studio:</span></span>

* <span data-ttu-id="872f6-216">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Datei *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="872f6-216">Right-click the *libman.json* file in **Solution Explorer**.</span></span>
* <span data-ttu-id="872f6-217">Wählen Sie die Option **Clientseitige Bibliotheken bereinigen** aus.</span><span class="sxs-lookup"><span data-stu-id="872f6-217">Select the **Clean Client-Side Libraries** option.</span></span>

<span data-ttu-id="872f6-218">Der Bereinigungsvorgang löscht keine vollständigen Verzeichnisse, um das unbeabsichtigte Entfernen von Nicht-Bibliotheksdateien zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="872f6-218">To prevent unintentional removal of non-library files, the clean operation doesn't delete whole directories.</span></span> <span data-ttu-id="872f6-219">Es werden nur Dateien entfernt, die bei der vorherigen Wiederherstellung enthalten waren.</span><span class="sxs-lookup"><span data-stu-id="872f6-219">It only removes files that were included in the previous restore.</span></span>

<span data-ttu-id="872f6-220">Während der Bereinigungsvorgang ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="872f6-220">While the clean operation is running:</span></span>

* <span data-ttu-id="872f6-221">Das Symbol für das Aufgabenstatuscenter in der Statusleiste von Visual Studio wird animiert und zeigt *Clientbibliotheksvorgang gestartet* an.</span><span class="sxs-lookup"><span data-stu-id="872f6-221">The TSC icon on the Visual Studio status bar will be animated and will read *Client libraries operation started*.</span></span> <span data-ttu-id="872f6-222">Wenn Sie auf das Symbol klicken, wird eine QuickInfo mit den bekannten Hintergrundaufgaben geöffnet.</span><span class="sxs-lookup"><span data-stu-id="872f6-222">Clicking the icon opens a tooltip listing the known background tasks.</span></span>
* <span data-ttu-id="872f6-223">Die Nachrichten werden an die Statusleiste und den **Bibliotheks-Manager**-Feed des Fensters **Ausgabe** gesendet.</span><span class="sxs-lookup"><span data-stu-id="872f6-223">Messages are sent to the status bar and the **Library Manager** feed of the **Output** window.</span></span> <span data-ttu-id="872f6-224">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="872f6-224">For example:</span></span>

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

<span data-ttu-id="872f6-225">Der Bereinigungsvorgang löscht nur Dateien aus dem Projekt.</span><span class="sxs-lookup"><span data-stu-id="872f6-225">The clean operation only deletes files from the project.</span></span> <span data-ttu-id="872f6-226">Bibliotheksdateien bleiben im Cache, um bei zukünftigen Wiederherstellungsvorgängen schneller abgerufen werden zu können.</span><span class="sxs-lookup"><span data-stu-id="872f6-226">Library files stay in the cache for faster retrieval on future restore operations.</span></span> <span data-ttu-id="872f6-227">Verwenden Sie zur Verwaltung von Bibliotheksdateien, die im Cache des lokalen Computers gespeichert sind, die [LibMan-Befehlszeilenschnittstelle](xref:client-side/libman/libman-cli).</span><span class="sxs-lookup"><span data-stu-id="872f6-227">To manage library files stored in the local machine's cache, use the [LibMan CLI](xref:client-side/libman/libman-cli).</span></span>

## <a name="uninstall-library-files"></a><span data-ttu-id="872f6-228">Deinstallieren von Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="872f6-228">Uninstall library files</span></span>

<span data-ttu-id="872f6-229">So deinstallieren Sie Bibliotheksdateien</span><span class="sxs-lookup"><span data-stu-id="872f6-229">To uninstall library files:</span></span>

* <span data-ttu-id="872f6-230">Öffnen Sie *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="872f6-230">Open *libman.json*.</span></span>
* <span data-ttu-id="872f6-231">Positionieren Sie die Einfügemarke innerhalb des entsprechenden `libraries`-Objektliterals.</span><span class="sxs-lookup"><span data-stu-id="872f6-231">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="872f6-232">Klicken Sie auf das Glühbirnensymbol, das am linken Rand angezeigt wird, und wählen Sie **Deinstallieren \<library_name>@\<library_version>** :</span><span class="sxs-lookup"><span data-stu-id="872f6-232">Click the light bulb icon that appears in the left margin, and select **Uninstall \<library_name>@\<library_version>**:</span></span>

  ![Kontextmenüoption zum Deinstallieren der Bibliothek](_static/uninstall-menu-option.png)

<span data-ttu-id="872f6-234">Alternativ können Sie das LibMan-Manifest (*libman.json*) manuell bearbeiten und speichern.</span><span class="sxs-lookup"><span data-stu-id="872f6-234">Alternatively, you can manually edit and save the LibMan manifest (*libman.json*).</span></span> <span data-ttu-id="872f6-235">Der [Wiederherstellungsvorgang](#restore-library-files) wird beim Speichern der Datei ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="872f6-235">The [restore operation](#restore-library-files) runs when the file is saved.</span></span> <span data-ttu-id="872f6-236">Bibliotheksdateien, die nicht mehr in *libman.json* definiert sind, werden aus dem Projekt entfernt.</span><span class="sxs-lookup"><span data-stu-id="872f6-236">Library files that are no longer defined in *libman.json* are removed from the project.</span></span>

## <a name="update-library-version"></a><span data-ttu-id="872f6-237">Aktualisieren der Bibliotheksversion</span><span class="sxs-lookup"><span data-stu-id="872f6-237">Update library version</span></span>

<span data-ttu-id="872f6-238">So suchen Sie nach einer aktualisierten Bibliotheksversion</span><span class="sxs-lookup"><span data-stu-id="872f6-238">To check for an updated library version:</span></span>

* <span data-ttu-id="872f6-239">Öffnen Sie *libman.json*.</span><span class="sxs-lookup"><span data-stu-id="872f6-239">Open *libman.json*.</span></span>
* <span data-ttu-id="872f6-240">Positionieren Sie die Einfügemarke innerhalb des entsprechenden `libraries`-Objektliterals.</span><span class="sxs-lookup"><span data-stu-id="872f6-240">Position the caret inside the corresponding `libraries` object literal.</span></span>
* <span data-ttu-id="872f6-241">Klicken Sie auf das Glühbirnensymbol, das am linken Rand angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="872f6-241">Click the light bulb icon that appears in the left margin.</span></span> <span data-ttu-id="872f6-242">Bewegen Sie den Mauszeiger über **Nach Updates suchen**.</span><span class="sxs-lookup"><span data-stu-id="872f6-242">Hover over **Check for updates**.</span></span>

<span data-ttu-id="872f6-243">LibMan sucht nach einer Bibliotheksversion, die neuer ist als die installierte Version.</span><span class="sxs-lookup"><span data-stu-id="872f6-243">LibMan checks for a library version newer than the version installed.</span></span> <span data-ttu-id="872f6-244">Die folgenden Ergebnisse können auftreten:</span><span class="sxs-lookup"><span data-stu-id="872f6-244">The following outcomes can occur:</span></span>

* <span data-ttu-id="872f6-245">Es wird die Nachricht **Keine Updates gefunden** angezeigt, wenn die aktuellste Version bereits installiert ist.</span><span class="sxs-lookup"><span data-stu-id="872f6-245">A **No updates found** message is displayed if the latest version is already installed.</span></span>
* <span data-ttu-id="872f6-246">Die aktuellste stabile Version wird angezeigt, falls sie nicht bereits installiert ist.</span><span class="sxs-lookup"><span data-stu-id="872f6-246">The latest stable version is displayed if not already installed.</span></span>

  ![Kontextmenüoption „Nach Updates suchen“](_static/update-menu-option.png)

* <span data-ttu-id="872f6-248">Wenn eine Vorabversion verfügbar ist, die aktueller ist als die installierte Version, wird die Vorabversion angezeigt.</span><span class="sxs-lookup"><span data-stu-id="872f6-248">If a pre-release newer than the installed version is available, the pre-release is displayed.</span></span>

<span data-ttu-id="872f6-249">Bearbeiten Sie die Datei *libman.json* manuell, um die Version auf eine ältere Bibliotheksversion herabzustufen.</span><span class="sxs-lookup"><span data-stu-id="872f6-249">To downgrade to an older library version, manually edit the *libman.json* file.</span></span> <span data-ttu-id="872f6-250">Wenn die Datei gespeichert wird, geht der LibMan-[Wiederherstellungsvorgang](#restore-library-files) wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="872f6-250">When the file is saved, the LibMan [restore operation](#restore-library-files):</span></span>

* <span data-ttu-id="872f6-251">Entfernt überflüssige Dateien aus der vorherigen Version.</span><span class="sxs-lookup"><span data-stu-id="872f6-251">Removes redundant files from the previous version.</span></span>
* <span data-ttu-id="872f6-252">Fügt neue und aktualisierte Dateien aus der neuen Version hinzu.</span><span class="sxs-lookup"><span data-stu-id="872f6-252">Adds new and updated files from the new version.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="872f6-253">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="872f6-253">Additional resources</span></span>

* <xref:client-side/libman/libman-cli>
* [<span data-ttu-id="872f6-254">GitHub-Repository für LibMan</span><span class="sxs-lookup"><span data-stu-id="872f6-254">LibMan GitHub repository</span></span>](https://github.com/aspnet/LibraryManager)
