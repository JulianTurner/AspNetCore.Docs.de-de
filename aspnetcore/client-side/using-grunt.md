---
title: Verwenden von Grunt in ASP.NET Core
author: rick-anderson
description: Verwenden von Grunt in ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
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
uid: client-side/using-grunt
ms.openlocfilehash: 374c23f440dcf301b3a1e1e9e6684dd050f218c6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054553"
---
# <a name="use-grunt-in-aspnet-core"></a><span data-ttu-id="af929-103">Verwenden von Grunt in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="af929-103">Use Grunt in ASP.NET Core</span></span>

<span data-ttu-id="af929-104">Grunt ist eine JavaScript-Aufgabenausführung, die die Skriptverkleinerung, TypeScript-Kompilierung, „lint“-Tools für die Codequalität, CSS-Präprozessoren und so gut wie jede sich wiederholende Aufgabe automatisiert, die zur Unterstützung der Cliententwicklung erledigt werden muss.</span><span class="sxs-lookup"><span data-stu-id="af929-104">Grunt is a JavaScript task runner that automates script minification, TypeScript compilation, code quality "lint" tools, CSS pre-processors, and just about any repetitive chore that needs doing to support client development.</span></span> <span data-ttu-id="af929-105">Grunt wird in Visual Studio vollständig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="af929-105">Grunt is fully supported in Visual Studio.</span></span>

<span data-ttu-id="af929-106">In diesem Beispiel wird ein leeres ASP.NET Core-Projekt als Ausgangspunkt verwendet, um zu zeigen, wie der Clientbuildprozess von Grund auf automatisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="af929-106">This example uses an empty ASP.NET Core project as its starting point, to show how to automate the client build process from scratch.</span></span>

<span data-ttu-id="af929-107">Das fertige Beispiel bereinigt das Zielbereitstellungsverzeichnis, kombiniert JavaScript-Dateien, prüft die Codequalität, verdichtet den Inhalt der JavaScript-Datei und stellt sie im Stammverzeichnis Ihrer Webanwendung bereit.</span><span class="sxs-lookup"><span data-stu-id="af929-107">The finished example cleans the target deployment directory, combines JavaScript files, checks code quality, condenses JavaScript file content and deploys to the root of your web application.</span></span> <span data-ttu-id="af929-108">Wir werden die folgenden Pakete verwenden:</span><span class="sxs-lookup"><span data-stu-id="af929-108">We will use the following packages:</span></span>

* <span data-ttu-id="af929-109">**grunt** : Das Grunt-Aufgabenausführungspaket.</span><span class="sxs-lookup"><span data-stu-id="af929-109">**grunt** : The Grunt task runner package.</span></span>

* <span data-ttu-id="af929-110">**grunt-contrib-clean** : Ein Plug-In, das Dateien oder Verzeichnisse entfernt.</span><span class="sxs-lookup"><span data-stu-id="af929-110">**grunt-contrib-clean** : A plugin that removes files or directories.</span></span>

* <span data-ttu-id="af929-111">**grunt-contrib-jshint** : Ein Plug-In, das die Qualität von JavaScript-Code überprüft.</span><span class="sxs-lookup"><span data-stu-id="af929-111">**grunt-contrib-jshint** : A plugin that reviews JavaScript code quality.</span></span>

* <span data-ttu-id="af929-112">**grunt-contrib-concat** : Ein Plug-In, das Dateien zu einer einzelnen Datei verknüpft.</span><span class="sxs-lookup"><span data-stu-id="af929-112">**grunt-contrib-concat** : A plugin that joins files into a single file.</span></span>

* <span data-ttu-id="af929-113">**grunt-contrib-uglify** : Ein Plug-In, das den Umfang von JavaScript verringert, um die Größe zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="af929-113">**grunt-contrib-uglify** : A plugin that minifies JavaScript to reduce size.</span></span>

* <span data-ttu-id="af929-114">**grunt-contrib-watch** : Ein Plug-In, das Dateiaktivitäten überwacht.</span><span class="sxs-lookup"><span data-stu-id="af929-114">**grunt-contrib-watch** : A plugin that watches file activity.</span></span>

## <a name="preparing-the-application"></a><span data-ttu-id="af929-115">Vorbereiten der Anwendung</span><span class="sxs-lookup"><span data-stu-id="af929-115">Preparing the application</span></span>

<span data-ttu-id="af929-116">Richten Sie zunächst eine neue leere Webanwendung ein, und fügen Sie TypeScript-Beispieldateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="af929-116">To begin, set up a new empty web application and add TypeScript example files.</span></span> <span data-ttu-id="af929-117">TypeScript-Dateien werden automatisch mit den Standardeinstellungen von Visual Studio in JavaScript kompiliert und sind unser Rohmaterial für die Verarbeitung mit Grunt.</span><span class="sxs-lookup"><span data-stu-id="af929-117">TypeScript files are automatically compiled into JavaScript using default Visual Studio settings and will be our raw material to process using Grunt.</span></span>

1. <span data-ttu-id="af929-118">Erstellen Sie in Visual Studio eine neue `ASP.NET Web Application`.</span><span class="sxs-lookup"><span data-stu-id="af929-118">In Visual Studio, create a new `ASP.NET Web Application`.</span></span>

2. <span data-ttu-id="af929-119">Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die ASP.NET Core-Vorlage **Leer** aus, und klicken Sie dann auf die Schaltfläche „OK“.</span><span class="sxs-lookup"><span data-stu-id="af929-119">In the **New ASP.NET Project** dialog, select the ASP.NET Core **Empty** template and click the OK button.</span></span>

3. <span data-ttu-id="af929-120">Überprüfen Sie im Projektmappen-Explorer die Projektstruktur.</span><span class="sxs-lookup"><span data-stu-id="af929-120">In the Solution Explorer, review the project structure.</span></span> <span data-ttu-id="af929-121">Der Ordner `\src` enthält leere `wwwroot`- und `Dependencies`-Knoten.</span><span class="sxs-lookup"><span data-stu-id="af929-121">The `\src` folder includes empty `wwwroot` and `Dependencies` nodes.</span></span>

    ![Leere Weblösung](using-grunt/_static/grunt-solution-explorer.png)

4. <span data-ttu-id="af929-123">Fügen Sie einen neuen Ordner namens `TypeScript` zu Ihrem Projektverzeichnis hinzu.</span><span class="sxs-lookup"><span data-stu-id="af929-123">Add a new folder named `TypeScript` to your project directory.</span></span>

5. <span data-ttu-id="af929-124">Stellen Sie vor dem Hinzufügen von Dateien sicher, dass in Visual Studio die Option „Beim Speichern kompilieren“ für TypeScript-Dateien aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="af929-124">Before adding any files, make sure that Visual Studio has the option 'compile on save' for TypeScript files checked.</span></span> <span data-ttu-id="af929-125">Navigieren Sie zu **Tools** > **Optionen** > **Text-Editor** > **TypeSkript** > **Projekt** :</span><span class="sxs-lookup"><span data-stu-id="af929-125">Navigate to **Tools** > **Options** > **Text Editor** > **Typescript** > **Project** :</span></span>

    ![Optionen zur Einstellung der automatischen Kompilierung von TypeScript-Dateien](using-grunt/_static/typescript-options.png)

6. <span data-ttu-id="af929-127">Klicken Sie mit der rechten Maustaste auf das Verzeichnis `TypeScript`, und wählen Sie **Hinzufügen > Neues Element** aus dem Kontextmenü aus.</span><span class="sxs-lookup"><span data-stu-id="af929-127">Right-click the `TypeScript` directory and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="af929-128">Wählen Sie das Element **JavaScript-Datei** aus, und benennen Sie die Datei *Tastes.ts* (beachten Sie die Erweiterung \*.ts).</span><span class="sxs-lookup"><span data-stu-id="af929-128">Select the **JavaScript file** item and name the file *Tastes.ts* (note the \*.ts extension).</span></span> <span data-ttu-id="af929-129">Kopieren Sie die folgende Zeile des TypeScript-Codes in die Datei (beim Speichern wird eine neue *Tastes.js* -Datei mit dem JavaScript-Quelltext angezeigt).</span><span class="sxs-lookup"><span data-stu-id="af929-129">Copy the line of TypeScript code below into the file (when you save, a new *Tastes.js* file will appear with the JavaScript source).</span></span>

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. <span data-ttu-id="af929-130">Fügen Sie eine zweite Datei zum Verzeichnis **TypeScript** hinzu, und nennen Sie sie `Food.ts`.</span><span class="sxs-lookup"><span data-stu-id="af929-130">Add a second file to the **TypeScript** directory and name it `Food.ts`.</span></span> <span data-ttu-id="af929-131">Kopieren Sie den nachfolgenden Code in die Datei.</span><span class="sxs-lookup"><span data-stu-id="af929-131">Copy the code below into the file.</span></span>

    ```typescript
    class Food {
      constructor(name: string, calories: number) {
        this._name = name;
        this._calories = calories;
      }

      private _name: string;
      get Name() {
        return this._name;
      }

      private _calories: number;
      get Calories() {
        return this._calories;
      }

      private _taste: Tastes;
      get Taste(): Tastes { return this._taste }
      set Taste(value: Tastes) {
        this._taste = value;
      }
    }
    ```

## <a name="configuring-npm"></a><span data-ttu-id="af929-132">Konfigurieren von NPM</span><span class="sxs-lookup"><span data-stu-id="af929-132">Configuring NPM</span></span>

<span data-ttu-id="af929-133">Als nächstes konfigurieren Sie NPM zum Herunterladen von „grunt“ und „grunt-tasks“.</span><span class="sxs-lookup"><span data-stu-id="af929-133">Next, configure NPM to download grunt and grunt-tasks.</span></span>

1. <span data-ttu-id="af929-134">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen > Neues Element** aus dem Kontextmenü aus.</span><span class="sxs-lookup"><span data-stu-id="af929-134">In the Solution Explorer, right-click the project and select **Add > New Item** from the context menu.</span></span> <span data-ttu-id="af929-135">Wählen Sie das Element **NPM-Konfigurationsdatei** aus, belassen Sie den Standardnamen, *package.json* , und klicken Sie auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="af929-135">Select the **NPM configuration file** item, leave the default name, *package.json* , and click the **Add** button.</span></span>

2. <span data-ttu-id="af929-136">Geben Sie in der Datei *package.json* innerhalb der `devDependencies`-Objektklammern „grunt“ ein.</span><span class="sxs-lookup"><span data-stu-id="af929-136">In the *package.json* file, inside the `devDependencies` object braces, enter "grunt".</span></span> <span data-ttu-id="af929-137">Wählen Sie `grunt` aus der Intellisense-Liste aus, und drücken Sie die EINGABETASTE.</span><span class="sxs-lookup"><span data-stu-id="af929-137">Select `grunt` from the Intellisense list and press the Enter key.</span></span> <span data-ttu-id="af929-138">Visual Studio zitiert den grunt-Paketnamen und fügt einen Doppelpunkt hinzu.</span><span class="sxs-lookup"><span data-stu-id="af929-138">Visual Studio will quote the grunt package name, and add a colon.</span></span> <span data-ttu-id="af929-139">Wählen Sie rechts neben dem Doppelpunkt die aktuellste stabile Version des Pakets oben in der Intellisense-Liste aus (drücken Sie `Ctrl-Space`, wenn Intellisense nicht angezeigt wird).</span><span class="sxs-lookup"><span data-stu-id="af929-139">To the right of the colon, select the latest stable version of the package from the top of the Intellisense list (press `Ctrl-Space` if Intellisense doesn't appear).</span></span>

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > <span data-ttu-id="af929-141">NPM verwendet die [semantische Versionsverwaltung](https://semver.org/), um Abhängigkeiten zu organisieren.</span><span class="sxs-lookup"><span data-stu-id="af929-141">NPM uses [semantic versioning](https://semver.org/) to organize dependencies.</span></span> <span data-ttu-id="af929-142">Die semantische Versionsverwaltung, auch als SemVer bezeichnet, identifiziert Pakete mit dem Nummerierungsschema \<major>.\<minor>.\<patch>. Intellisense vereinfacht die semantische Versionsverwaltung, indem es nur einige wenige gemeinsame Optionen anzeigt.</span><span class="sxs-lookup"><span data-stu-id="af929-142">Semantic versioning, also known as SemVer, identifies packages with the numbering scheme \<major>.\<minor>.\<patch>. Intellisense simplifies semantic versioning by showing only a few common choices.</span></span> <span data-ttu-id="af929-143">Der oberste Punkt in der Intellisense-Liste (0.4.5 im obigen Beispiel) gilt als die aktuellste stabile Version des Pakets.</span><span class="sxs-lookup"><span data-stu-id="af929-143">The top item in the Intellisense list (0.4.5 in the example above) is considered the latest stable version of the package.</span></span> <span data-ttu-id="af929-144">Das Caretzeichen (^) entspricht der aktuellsten Hauptversion und die Tilde (~) entspricht der aktuellsten Nebenversion.</span><span class="sxs-lookup"><span data-stu-id="af929-144">The caret (^) symbol matches the most recent major version and the tilde (~) matches the most recent minor version.</span></span> <span data-ttu-id="af929-145">Eine Anleitung für die volle Ausdrucksfähigkeit, die SemVer bietet, finden Sie in der [NPM SemVer Version-Parserreferenz](https://www.npmjs.com/package/semver).</span><span class="sxs-lookup"><span data-stu-id="af929-145">See the [NPM semver version parser reference](https://www.npmjs.com/package/semver) as a guide to the full expressivity that SemVer provides.</span></span>

3. <span data-ttu-id="af929-146">Fügen Sie weitere Abhängigkeiten hinzu, um grunt-contrib-\*-Pakete für *clean* , *jshint* , *concat* , *uglify* und *watch* zu laden, wie im Beispiel unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="af929-146">Add more dependencies to load grunt-contrib-\* packages for *clean* , *jshint* , *concat* , *uglify* , and *watch* as shown in the example below.</span></span> <span data-ttu-id="af929-147">Die Versionen müssen nicht mit dem Beispiel übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="af929-147">The versions don't need to match the example.</span></span>

    ```json
    "devDependencies": {
      "grunt": "0.4.5",
      "grunt-contrib-clean": "0.6.0",
      "grunt-contrib-jshint": "0.11.0",
      "grunt-contrib-concat": "0.5.1",
      "grunt-contrib-uglify": "0.8.0",
      "grunt-contrib-watch": "0.6.1"
    }
    ```

4. <span data-ttu-id="af929-148">Speichern Sie die Datei *package.json*.</span><span class="sxs-lookup"><span data-stu-id="af929-148">Save the *package.json* file.</span></span>

<span data-ttu-id="af929-149">Die Pakete für die einzelnen `devDependencies`-Elemente werden zusammen mit den für jedes Paket erforderlichen Dateien heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="af929-149">The packages for each `devDependencies` item will download, along with any files that each package requires.</span></span> <span data-ttu-id="af929-150">Sie finden die Paketdateien im Verzeichnis *node_modules* , indem Sie die Schaltfläche **Alle Dateien anzeigen** im **Projektmappen-Explorer** aktivieren.</span><span class="sxs-lookup"><span data-stu-id="af929-150">You can find the package files in the *node_modules* directory by enabling the **Show All Files** button in **Solution Explorer**.</span></span>

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> <span data-ttu-id="af929-152">Bei Bedarf können Sie Abhängigkeiten im **Projektmappen-Explorer** manuell wiederherstellen, indem Sie mit der rechten Maustaste auf `Dependencies\NPM` klicken und die Menüoption **Pakete wiederherstellen** auswählen.</span><span class="sxs-lookup"><span data-stu-id="af929-152">If you need to, you can manually restore dependencies in **Solution Explorer** by right-clicking on `Dependencies\NPM` and selecting the **Restore Packages** menu option.</span></span>

![Wiederherstellen von Paketen](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a><span data-ttu-id="af929-154">Konfigurieren von Grunt</span><span class="sxs-lookup"><span data-stu-id="af929-154">Configuring Grunt</span></span>

<span data-ttu-id="af929-155">Grunt wird mithilfe eines Manifests namens *Gruntfile.js* konfiguriert, das Aufgaben definiert, lädt und registriert, die manuell ausgeführt oder so konfiguriert werden können, dass sie automatisch auf der Grundlage von Ereignissen in Visual Studio ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="af929-155">Grunt is configured using a manifest named *Gruntfile.js* that defines, loads and registers tasks that can be run manually or configured to run automatically based on events in Visual Studio.</span></span>

1. <span data-ttu-id="af929-156">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Neues Element** aus.</span><span class="sxs-lookup"><span data-stu-id="af929-156">Right-click the project and select **Add** > **New Item**.</span></span> <span data-ttu-id="af929-157">Wählen Sie die Elementvorlage **JavaScript-Datei** aus, ändern Sie den Namen in *Gruntfile.js* , und klicken Sie auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="af929-157">Select the **JavaScript File** item template, change the name to *Gruntfile.js* , and click the **Add** button.</span></span>

1. <span data-ttu-id="af929-158">Fügen Sie den folgenden Code zu *Gruntfile.js* hinzu.</span><span class="sxs-lookup"><span data-stu-id="af929-158">Add the following code to *Gruntfile.js*.</span></span> <span data-ttu-id="af929-159">Die Funktion `initConfig` legt Optionen für jedes Paket fest, und der Rest des Moduls lädt und registriert Aufgaben.</span><span class="sxs-lookup"><span data-stu-id="af929-159">The `initConfig` function sets options for each package, and the remainder of the module loads and register tasks.</span></span>

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. <span data-ttu-id="af929-160">Fügen Sie innerhalb der Funktion `initConfig` Optionen für die Aufgabe `clean` hinzu, wie im *Gruntfile.js* -Beispiel unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="af929-160">Inside the `initConfig` function, add options for the `clean` task as shown in the example *Gruntfile.js* below.</span></span> <span data-ttu-id="af929-161">Die Aufgabe `clean` akzeptiert ein Array von Verzeichniszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="af929-161">The `clean` task accepts an array of directory strings.</span></span> <span data-ttu-id="af929-162">Diese Aufgabe entfernt Dateien aus *wwwroot/lib* und entfernt das gesamte Verzeichnis */temp*.</span><span class="sxs-lookup"><span data-stu-id="af929-162">This task removes files from *wwwroot/lib* and removes the entire */temp* directory.</span></span>

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. <span data-ttu-id="af929-163">Fügen Sie unterhalb der Funktion `initConfig` einen Aufruf von `grunt.loadNpmTasks` hinzu.</span><span class="sxs-lookup"><span data-stu-id="af929-163">Below the `initConfig` function, add a call to `grunt.loadNpmTasks`.</span></span> <span data-ttu-id="af929-164">Dadurch wird die Aufgabe von Visual Studio aus ausführbar.</span><span class="sxs-lookup"><span data-stu-id="af929-164">This will make the task runnable from Visual Studio.</span></span>

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. <span data-ttu-id="af929-165">Speichern Sie *Grundfile.js*.</span><span class="sxs-lookup"><span data-stu-id="af929-165">Save *Gruntfile.js*.</span></span> <span data-ttu-id="af929-166">Die Datei sollte in etwa so aussehen wie der Screenshot unten.</span><span class="sxs-lookup"><span data-stu-id="af929-166">The file should look something like the screenshot below.</span></span>

    ![Anfängliche Gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. <span data-ttu-id="af929-168">Klicken Sie mit der rechten Maustaste auf *Gruntfile.js* , und wählen Sie **Aufgabenausführungs-Explorer** aus dem Kontextmenü aus.</span><span class="sxs-lookup"><span data-stu-id="af929-168">Right-click *Gruntfile.js* and select **Task Runner Explorer** from the context menu.</span></span> <span data-ttu-id="af929-169">Das Fenster **Aufgabenausführungs-Explorer** wird geöffnet.</span><span class="sxs-lookup"><span data-stu-id="af929-169">The **Task Runner Explorer** window will open.</span></span>

    ![Menü des Aufgabenausführungs-Explorers](using-grunt/_static/task-runner-explorer-menu.png)

1. <span data-ttu-id="af929-171">Stellen Sie sicher, dass `clean` unter **Aufgaben** im **Aufgabenausführungs-Explorer** angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="af929-171">Verify that `clean` shows under **Tasks** in the **Task Runner Explorer**.</span></span>

    ![Aufgabenliste des Aufgabenausführungs-Explorers](using-grunt/_static/task-runner-explorer-tasks.png)

1. <span data-ttu-id="af929-173">Klicken Sie mit der rechten Maustaste auf die bereinigte Aufgabe, und wählen Sie **Ausführen** aus dem Kontextmenü aus.</span><span class="sxs-lookup"><span data-stu-id="af929-173">Right-click the clean task and select **Run** from the context menu.</span></span> <span data-ttu-id="af929-174">Ein Befehlsfenster zeigt den Fortschritt der Aufgabe an.</span><span class="sxs-lookup"><span data-stu-id="af929-174">A command window displays progress of the task.</span></span>

    ![Aufgabenausführungs-Explorer – Ausführen der bereinigten Aufgabe](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > <span data-ttu-id="af929-176">Es sind noch keine Dateien oder Verzeichnisse zum Bereinigen vorhanden.</span><span class="sxs-lookup"><span data-stu-id="af929-176">There are no files or directories to clean yet.</span></span> <span data-ttu-id="af929-177">Bei Bedarf können Sie diese manuell im Projektmappen-Explorer erstellen und dann die bereinigte Aufgabe als Test ausführen.</span><span class="sxs-lookup"><span data-stu-id="af929-177">If you like, you can manually create them in the Solution Explorer and then run the clean task as a test.</span></span>

1. <span data-ttu-id="af929-178">Fügen Sie in der Funktion `initConfig` einen Eintrag für `concat` mit dem folgenden Code ein.</span><span class="sxs-lookup"><span data-stu-id="af929-178">In the `initConfig` function, add an entry for `concat` using the code below.</span></span>

    <span data-ttu-id="af929-179">Das Eigenschaftenarray `src` listet die zu kombinierenden Dateien in der Reihenfolge auf, in der sie kombiniert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="af929-179">The `src` property array lists files to combine, in the order that they should be combined.</span></span> <span data-ttu-id="af929-180">Die Eigenschaft `dest` weist den Pfad zu der erzeugten kombinierten Datei zu.</span><span class="sxs-lookup"><span data-stu-id="af929-180">The `dest` property assigns the path to the combined file that's produced.</span></span>

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="af929-181">Die Eigenschaft `all` im obigen Code ist der Name eines Ziels.</span><span class="sxs-lookup"><span data-stu-id="af929-181">The `all` property in the code above is the name of a target.</span></span> <span data-ttu-id="af929-182">In einigen Grunt-Aufgaben werden Ziele verwendet, um mehrere Buildumgebungen zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="af929-182">Targets are used in some Grunt tasks to allow multiple build environments.</span></span> <span data-ttu-id="af929-183">Sie können die integrierten Ziele mit IntelliSense anzeigen oder eigene Ziele zuweisen.</span><span class="sxs-lookup"><span data-stu-id="af929-183">You can view the built-in targets using IntelliSense or assign your own.</span></span>

1. <span data-ttu-id="af929-184">Fügen Sie die Aufgabe `jshint` mit dem folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="af929-184">Add the `jshint` task using the code below.</span></span>

    <span data-ttu-id="af929-185">Das Hilfsprogramm „jshint `code-quality`“ wird für jede JavaScript-Datei ausgeführt, die im Verzeichnis *temp* gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="af929-185">The jshint `code-quality` utility is run against every JavaScript file found in the *temp* directory.</span></span>

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > <span data-ttu-id="af929-186">Die Option „-W069“ ist ein Fehler, der von jshint erzeugt wird, wenn JavaScript die Klammersyntax verwendet, um eine Eigenschaft anstelle der Punktnotation zuzuweisen, d. h. `Tastes["Sweet"]` anstelle von `Tastes.Sweet`.</span><span class="sxs-lookup"><span data-stu-id="af929-186">The option "-W069" is an error produced by jshint when JavaScript uses bracket syntax to assign a property instead of dot notation, i.e. `Tastes["Sweet"]` instead of `Tastes.Sweet`.</span></span> <span data-ttu-id="af929-187">Die Option deaktiviert die Warnung, damit der Rest des Prozesses fortgesetzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="af929-187">The option turns off the warning to allow the rest of the process to continue.</span></span>

1. <span data-ttu-id="af929-188">Fügen Sie die Aufgabe `uglify` mit dem folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="af929-188">Add the `uglify` task using the code below.</span></span>

    <span data-ttu-id="af929-189">Die Aufgabe verkleinert die Datei *combined.js* , die sich im Verzeichnis „temp“ befindet, und erstellt die Ergebnisdatei in „wwwroot/lib“ gemäß der Standardnamenskonvention *\<file name\>.min.js*.</span><span class="sxs-lookup"><span data-stu-id="af929-189">The task minifies the *combined.js* file found in the temp directory and creates the result file in wwwroot/lib following the standard naming convention *\<file name\>.min.js*.</span></span>

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. <span data-ttu-id="af929-190">Fügen Sie unter dem Aufruf von `grunt.loadNpmTasks`, der `grunt-contrib-clean` lädt, den gleichen Aufruf für „jshint“, „concat“ und „uglify“ ein, indem Sie den folgenden Code verwenden.</span><span class="sxs-lookup"><span data-stu-id="af929-190">Under the call to `grunt.loadNpmTasks` that loads `grunt-contrib-clean`, include the same call for jshint, concat, and uglify using the code below.</span></span>

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. <span data-ttu-id="af929-191">Speichern Sie *Grundfile.js*.</span><span class="sxs-lookup"><span data-stu-id="af929-191">Save *Gruntfile.js*.</span></span> <span data-ttu-id="af929-192">Die Datei sollte in etwa so aussehen wie das Beispiel unten.</span><span class="sxs-lookup"><span data-stu-id="af929-192">The file should look something like the example below.</span></span>

    ![Beispiel für eine vollständige Grunt-Datei](using-grunt/_static/gruntfile-js-complete.png)

1. <span data-ttu-id="af929-194">Beachten Sie, dass die Aufgabenliste des **Aufgabenausführungs-Explorers** `clean`-, `concat`-, `jshint`- und `uglify`-Aufgaben enthält.</span><span class="sxs-lookup"><span data-stu-id="af929-194">Notice that the **Task Runner Explorer** Tasks list includes `clean`, `concat`, `jshint` and `uglify` tasks.</span></span> <span data-ttu-id="af929-195">Führen Sie jede Aufgabe der Reihe nach aus, und beobachten Sie die Ergebnisse im **Projektmappen-Explorer**.</span><span class="sxs-lookup"><span data-stu-id="af929-195">Run each task in order and observe the results in **Solution Explorer**.</span></span> <span data-ttu-id="af929-196">Jede Aufgabe sollte ohne Fehler ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="af929-196">Each task should run without errors.</span></span>

    ![Aufgabenausführungs-Explorer – Ausführen der einzelnen Aufgaben](using-grunt/_static/task-runner-explorer-run-each-task.png)

    <span data-ttu-id="af929-198">Die concat-Aufgabe erstellt eine neue *combined.js* -Datei und legt sie im Verzeichnis „temp“ ab.</span><span class="sxs-lookup"><span data-stu-id="af929-198">The concat task creates a new *combined.js* file and places it into the temp directory.</span></span> <span data-ttu-id="af929-199">Die `jshint`-Aufgabe wird einfach ausgeführt und erzeugt keine Ausgabe.</span><span class="sxs-lookup"><span data-stu-id="af929-199">The `jshint` task simply runs and doesn't produce output.</span></span> <span data-ttu-id="af929-200">Die `uglify`-Aufgabe erstellt eine neue *combined.min.js* -Datei und platziert sie in *wwwroot/lib*.</span><span class="sxs-lookup"><span data-stu-id="af929-200">The `uglify` task creates a new *combined.min.js* file and places it into *wwwroot/lib*.</span></span> <span data-ttu-id="af929-201">Nach der Fertigstellung sollte die Lösung in etwa so aussehen wie der Screenshot unten:</span><span class="sxs-lookup"><span data-stu-id="af929-201">On completion, the solution should look something like the screenshot below:</span></span>

    ![Projektmappen-Explorer nach allen Aufgaben](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > <span data-ttu-id="af929-203">Weitere Informationen zu den Optionen für die einzelnen Pakete finden Sie unter [https://www.npmjs.com/](https://www.npmjs.com/), und suchen Sie den Paketnamen im Suchfeld auf der Hauptseite.</span><span class="sxs-lookup"><span data-stu-id="af929-203">For more information on the options for each package, visit [https://www.npmjs.com/](https://www.npmjs.com/) and lookup the package name in the search box on the main page.</span></span> <span data-ttu-id="af929-204">Sie können z. B. das Paket grunt-contrib-clean suchen, um einen Dokumentationslink zu erhalten, der alle seine Parameter erläutert.</span><span class="sxs-lookup"><span data-stu-id="af929-204">For example, you can look up the grunt-contrib-clean package to get a documentation link that explains all of its parameters.</span></span>

### <a name="all-together-now"></a><span data-ttu-id="af929-205">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="af929-205">All together now</span></span>

<span data-ttu-id="af929-206">Verwenden Sie die Grunt `registerTask()`-Methode, um eine Reihe von Aufgaben in einer bestimmten Reihenfolge auszuführen.</span><span class="sxs-lookup"><span data-stu-id="af929-206">Use the Grunt `registerTask()` method to run a series of tasks in a particular sequence.</span></span> <span data-ttu-id="af929-207">Um z. B. die obigen Beispielschritte in der Reihenfolge clean -> concat -> jshint -> uglify auszuführen, fügen Sie den folgenden Code zum Modul hinzu.</span><span class="sxs-lookup"><span data-stu-id="af929-207">For example, to run the example steps above in the order clean -> concat -> jshint -> uglify, add the code below to the module.</span></span> <span data-ttu-id="af929-208">Der Code sollte auf derselben Ebene wie die loadNpmTasks()-Aufrufe außerhalb von initConfig hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="af929-208">The code should be added to the same level as the loadNpmTasks() calls, outside initConfig.</span></span>

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

<span data-ttu-id="af929-209">Die neue Aufgabe wird im Aufgabenausführungs-Explorer unter „Aliastasks“ angezeigt.</span><span class="sxs-lookup"><span data-stu-id="af929-209">The new task shows up in Task Runner Explorer under Alias Tasks.</span></span> <span data-ttu-id="af929-210">Sie können sie mit der rechten Maustaste anklicken und genau wie andere Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="af929-210">You can right-click and run it just as you would other tasks.</span></span> <span data-ttu-id="af929-211">Die Aufgabe `all` führt `clean`, `concat`, `jshint` und `uglify` in dieser Reihenfolge aus.</span><span class="sxs-lookup"><span data-stu-id="af929-211">The `all` task will run `clean`, `concat`, `jshint` and `uglify`, in order.</span></span>

![Alias Grunt-Aufgaben](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a><span data-ttu-id="af929-213">Erkennen von Änderungen</span><span class="sxs-lookup"><span data-stu-id="af929-213">Watching for changes</span></span>

<span data-ttu-id="af929-214">Eine `watch`-Aufgabe überwacht Dateien und Verzeichnisse.</span><span class="sxs-lookup"><span data-stu-id="af929-214">A `watch` task keeps an eye on files and directories.</span></span> <span data-ttu-id="af929-215">Das Überwachungselement löst automatisch Aufgaben aus, wenn es Änderungen feststellt.</span><span class="sxs-lookup"><span data-stu-id="af929-215">The watch triggers tasks automatically if it detects changes.</span></span> <span data-ttu-id="af929-216">Fügen Sie den folgenden Code zu initConfig hinzu, um auf Änderungen an \*.js-Dateien im TypeScript-Verzeichnis zu achten.</span><span class="sxs-lookup"><span data-stu-id="af929-216">Add the code below to initConfig to watch for changes to \*.js files in the TypeScript directory.</span></span> <span data-ttu-id="af929-217">Wenn eine JavaScript-Datei geändert wird, führt `watch` die Aufgabe `all` aus.</span><span class="sxs-lookup"><span data-stu-id="af929-217">If a JavaScript file is changed, `watch` will run the `all` task.</span></span>

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

<span data-ttu-id="af929-218">Fügen Sie einen Aufruf an `loadNpmTasks()` hinzu, um die Aufgabe `watch` im Aufgabenausführungs-Explorer anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="af929-218">Add a call to `loadNpmTasks()` to show the `watch` task in Task Runner Explorer.</span></span>

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

<span data-ttu-id="af929-219">Klicken Sie mit der rechten Maustaste auf das Überwachungselement im Aufgabenausführungs-Explorer, und wählen Sie im Kontextmenü „Ausführen“ aus.</span><span class="sxs-lookup"><span data-stu-id="af929-219">Right-click the watch task in Task Runner Explorer and select Run from the context menu.</span></span> <span data-ttu-id="af929-220">Im Befehlsfenster, das die aktive Überwachungsaufgabe anzeigt, wird die Meldung „Warten...“ angezeigt.</span><span class="sxs-lookup"><span data-stu-id="af929-220">The command window that shows the watch task running will display a "Waiting…" message.</span></span> <span data-ttu-id="af929-221">Öffnen Sie eine der TypeScript-Dateien, fügen Sie ein Leerzeichen hinzu, und speichern Sie dann die Datei.</span><span class="sxs-lookup"><span data-stu-id="af929-221">Open one of the TypeScript files, add a space, and then save the file.</span></span> <span data-ttu-id="af929-222">Dadurch wird das Überwachungselement ausgelöst und die anderen Aufgaben werden der Reihe nach ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="af929-222">This will trigger the watch task and trigger the other tasks to run in order.</span></span> <span data-ttu-id="af929-223">Der folgende Screenshot zeigt eine Beispielausführung.</span><span class="sxs-lookup"><span data-stu-id="af929-223">The screenshot below shows a sample run.</span></span>

![Ausgabe aktiver Aufgaben](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a><span data-ttu-id="af929-225">Binden an Visual Studio-Ereignisse</span><span class="sxs-lookup"><span data-stu-id="af929-225">Binding to Visual Studio events</span></span>

<span data-ttu-id="af929-226">Wenn Sie Ihre Aufgaben nicht jedes Mal, wenn Sie in Visual Studio arbeiten, manuell starten möchten, binden Sie Aufgaben an die Ereignisse **Vor Build** , **Nach Build** , **Bereinigen** und **Projekt geöffnet**.</span><span class="sxs-lookup"><span data-stu-id="af929-226">Unless you want to manually start your tasks every time you work in Visual Studio, bind tasks to **Before Build** , **After Build** , **Clean** , and **Project Open** events.</span></span>

<span data-ttu-id="af929-227">Binden Sie `watch`, sodass es bei jedem Öffnen von Visual Studio ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="af929-227">Bind `watch` so that it runs every time Visual Studio opens.</span></span> <span data-ttu-id="af929-228">Klicken Sie im Aufgabenausführungs-Explorer mit der rechten Maustaste auf das Überwachungselement, und wählen Sie **Bindungen** > **Projekt geöffnet** aus dem Kontextmenü aus.</span><span class="sxs-lookup"><span data-stu-id="af929-228">In Task Runner Explorer, right-click the watch task and select **Bindings** > **Project Open** from the context menu.</span></span>

![Binden einer Aufgabe an das Öffnen von Projekten](using-grunt/_static/bindings-project-open.png)

<span data-ttu-id="af929-230">Entladen Sie das Projekt, und laden Sie es erneut.</span><span class="sxs-lookup"><span data-stu-id="af929-230">Unload and reload the project.</span></span> <span data-ttu-id="af929-231">Wenn das Projekt wieder geladen wird, wird das Überwachungselement automatisch asugeführt.</span><span class="sxs-lookup"><span data-stu-id="af929-231">When the project loads again, the watch task starts running automatically.</span></span>

## <a name="summary"></a><span data-ttu-id="af929-232">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="af929-232">Summary</span></span>

<span data-ttu-id="af929-233">Grunt ist eine leistungsstarke Aufgabenausführung, mit der sich die meisten Aufgaben für Clientbuilds automatisieren lassen.</span><span class="sxs-lookup"><span data-stu-id="af929-233">Grunt is a powerful task runner that can be used to automate most client-build tasks.</span></span> <span data-ttu-id="af929-234">Grunt nutzt NPM zur Bereitstellung seiner Pakete und bietet eine Toolintegration mit Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="af929-234">Grunt leverages NPM to deliver its packages, and features tooling integration with Visual Studio.</span></span> <span data-ttu-id="af929-235">Der Aufgabenausführungs-Explorer von Visual Studio erkennt Änderungen an Konfigurationsdateien und bietet eine bequeme Schnittstelle zum Ausführen von Aufgaben, zum Anzeigen aktiver Aufgaben und zum Binden von Aufgaben an Visual Studio-Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="af929-235">Visual Studio's Task Runner Explorer detects changes to configuration files and provides a convenient interface to run tasks, view running tasks, and bind tasks to Visual Studio events.</span></span>
