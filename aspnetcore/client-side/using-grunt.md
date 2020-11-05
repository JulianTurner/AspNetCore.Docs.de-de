---
title: Verwenden von Grunt in ASP.NET Core
author: rick-anderson
description: Verwenden von Grunt in ASP.NET Core
ms.author: riande
ms.date: 12/05/2019
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
uid: client-side/using-grunt
ms.openlocfilehash: 374c23f440dcf301b3a1e1e9e6684dd050f218c6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054553"
---
# <a name="use-grunt-in-aspnet-core"></a>Verwenden von Grunt in ASP.NET Core

Grunt ist eine JavaScript-Aufgabenausführung, die die Skriptverkleinerung, TypeScript-Kompilierung, „lint“-Tools für die Codequalität, CSS-Präprozessoren und so gut wie jede sich wiederholende Aufgabe automatisiert, die zur Unterstützung der Cliententwicklung erledigt werden muss. Grunt wird in Visual Studio vollständig unterstützt.

In diesem Beispiel wird ein leeres ASP.NET Core-Projekt als Ausgangspunkt verwendet, um zu zeigen, wie der Clientbuildprozess von Grund auf automatisiert werden kann.

Das fertige Beispiel bereinigt das Zielbereitstellungsverzeichnis, kombiniert JavaScript-Dateien, prüft die Codequalität, verdichtet den Inhalt der JavaScript-Datei und stellt sie im Stammverzeichnis Ihrer Webanwendung bereit. Wir werden die folgenden Pakete verwenden:

* **grunt** : Das Grunt-Aufgabenausführungspaket.

* **grunt-contrib-clean** : Ein Plug-In, das Dateien oder Verzeichnisse entfernt.

* **grunt-contrib-jshint** : Ein Plug-In, das die Qualität von JavaScript-Code überprüft.

* **grunt-contrib-concat** : Ein Plug-In, das Dateien zu einer einzelnen Datei verknüpft.

* **grunt-contrib-uglify** : Ein Plug-In, das den Umfang von JavaScript verringert, um die Größe zu reduzieren.

* **grunt-contrib-watch** : Ein Plug-In, das Dateiaktivitäten überwacht.

## <a name="preparing-the-application"></a>Vorbereiten der Anwendung

Richten Sie zunächst eine neue leere Webanwendung ein, und fügen Sie TypeScript-Beispieldateien hinzu. TypeScript-Dateien werden automatisch mit den Standardeinstellungen von Visual Studio in JavaScript kompiliert und sind unser Rohmaterial für die Verarbeitung mit Grunt.

1. Erstellen Sie in Visual Studio eine neue `ASP.NET Web Application`.

2. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die ASP.NET Core-Vorlage **Leer** aus, und klicken Sie dann auf die Schaltfläche „OK“.

3. Überprüfen Sie im Projektmappen-Explorer die Projektstruktur. Der Ordner `\src` enthält leere `wwwroot`- und `Dependencies`-Knoten.

    ![Leere Weblösung](using-grunt/_static/grunt-solution-explorer.png)

4. Fügen Sie einen neuen Ordner namens `TypeScript` zu Ihrem Projektverzeichnis hinzu.

5. Stellen Sie vor dem Hinzufügen von Dateien sicher, dass in Visual Studio die Option „Beim Speichern kompilieren“ für TypeScript-Dateien aktiviert ist. Navigieren Sie zu **Tools** > **Optionen** > **Text-Editor** > **TypeSkript** > **Projekt** :

    ![Optionen zur Einstellung der automatischen Kompilierung von TypeScript-Dateien](using-grunt/_static/typescript-options.png)

6. Klicken Sie mit der rechten Maustaste auf das Verzeichnis `TypeScript`, und wählen Sie **Hinzufügen > Neues Element** aus dem Kontextmenü aus. Wählen Sie das Element **JavaScript-Datei** aus, und benennen Sie die Datei *Tastes.ts* (beachten Sie die Erweiterung \*.ts). Kopieren Sie die folgende Zeile des TypeScript-Codes in die Datei (beim Speichern wird eine neue *Tastes.js* -Datei mit dem JavaScript-Quelltext angezeigt).

    ```typescript
    enum Tastes { Sweet, Sour, Salty, Bitter }
    ```

7. Fügen Sie eine zweite Datei zum Verzeichnis **TypeScript** hinzu, und nennen Sie sie `Food.ts`. Kopieren Sie den nachfolgenden Code in die Datei.

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

## <a name="configuring-npm"></a>Konfigurieren von NPM

Als nächstes konfigurieren Sie NPM zum Herunterladen von „grunt“ und „grunt-tasks“.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen > Neues Element** aus dem Kontextmenü aus. Wählen Sie das Element **NPM-Konfigurationsdatei** aus, belassen Sie den Standardnamen, *package.json* , und klicken Sie auf die Schaltfläche **Hinzufügen**.

2. Geben Sie in der Datei *package.json* innerhalb der `devDependencies`-Objektklammern „grunt“ ein. Wählen Sie `grunt` aus der Intellisense-Liste aus, und drücken Sie die EINGABETASTE. Visual Studio zitiert den grunt-Paketnamen und fügt einen Doppelpunkt hinzu. Wählen Sie rechts neben dem Doppelpunkt die aktuellste stabile Version des Pakets oben in der Intellisense-Liste aus (drücken Sie `Ctrl-Space`, wenn Intellisense nicht angezeigt wird).

    ![grunt Intellisense](using-grunt/_static/devdependencies-grunt.png)

    > [!NOTE]
    > NPM verwendet die [semantische Versionsverwaltung](https://semver.org/), um Abhängigkeiten zu organisieren. Die semantische Versionsverwaltung, auch als SemVer bezeichnet, identifiziert Pakete mit dem Nummerierungsschema \<major>.\<minor>.\<patch>. Intellisense vereinfacht die semantische Versionsverwaltung, indem es nur einige wenige gemeinsame Optionen anzeigt. Der oberste Punkt in der Intellisense-Liste (0.4.5 im obigen Beispiel) gilt als die aktuellste stabile Version des Pakets. Das Caretzeichen (^) entspricht der aktuellsten Hauptversion und die Tilde (~) entspricht der aktuellsten Nebenversion. Eine Anleitung für die volle Ausdrucksfähigkeit, die SemVer bietet, finden Sie in der [NPM SemVer Version-Parserreferenz](https://www.npmjs.com/package/semver).

3. Fügen Sie weitere Abhängigkeiten hinzu, um grunt-contrib-\*-Pakete für *clean* , *jshint* , *concat* , *uglify* und *watch* zu laden, wie im Beispiel unten gezeigt. Die Versionen müssen nicht mit dem Beispiel übereinstimmen.

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

4. Speichern Sie die Datei *package.json*.

Die Pakete für die einzelnen `devDependencies`-Elemente werden zusammen mit den für jedes Paket erforderlichen Dateien heruntergeladen. Sie finden die Paketdateien im Verzeichnis *node_modules* , indem Sie die Schaltfläche **Alle Dateien anzeigen** im **Projektmappen-Explorer** aktivieren.

![grunt node_modules](using-grunt/_static/node-modules.png)

> [!NOTE]
> Bei Bedarf können Sie Abhängigkeiten im **Projektmappen-Explorer** manuell wiederherstellen, indem Sie mit der rechten Maustaste auf `Dependencies\NPM` klicken und die Menüoption **Pakete wiederherstellen** auswählen.

![Wiederherstellen von Paketen](using-grunt/_static/restore-packages.png)

## <a name="configuring-grunt"></a>Konfigurieren von Grunt

Grunt wird mithilfe eines Manifests namens *Gruntfile.js* konfiguriert, das Aufgaben definiert, lädt und registriert, die manuell ausgeführt oder so konfiguriert werden können, dass sie automatisch auf der Grundlage von Ereignissen in Visual Studio ausgeführt werden.

1. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Neues Element** aus. Wählen Sie die Elementvorlage **JavaScript-Datei** aus, ändern Sie den Namen in *Gruntfile.js* , und klicken Sie auf die Schaltfläche **Hinzufügen**.

1. Fügen Sie den folgenden Code zu *Gruntfile.js* hinzu. Die Funktion `initConfig` legt Optionen für jedes Paket fest, und der Rest des Moduls lädt und registriert Aufgaben.

   ```javascript
   module.exports = function (grunt) {
     grunt.initConfig({
     });
   };
   ```

1. Fügen Sie innerhalb der Funktion `initConfig` Optionen für die Aufgabe `clean` hinzu, wie im *Gruntfile.js* -Beispiel unten gezeigt. Die Aufgabe `clean` akzeptiert ein Array von Verzeichniszeichenfolgen. Diese Aufgabe entfernt Dateien aus *wwwroot/lib* und entfernt das gesamte Verzeichnis */temp*.

    ```javascript
    module.exports = function (grunt) {
      grunt.initConfig({
        clean: ["wwwroot/lib/*", "temp/"],
      });
    };
    ```

1. Fügen Sie unterhalb der Funktion `initConfig` einen Aufruf von `grunt.loadNpmTasks` hinzu. Dadurch wird die Aufgabe von Visual Studio aus ausführbar.

    ```javascript
    grunt.loadNpmTasks("grunt-contrib-clean");
    ```

1. Speichern Sie *Grundfile.js*. Die Datei sollte in etwa so aussehen wie der Screenshot unten.

    ![Anfängliche Gruntfile](using-grunt/_static/gruntfile-js-initial.png)

1. Klicken Sie mit der rechten Maustaste auf *Gruntfile.js* , und wählen Sie **Aufgabenausführungs-Explorer** aus dem Kontextmenü aus. Das Fenster **Aufgabenausführungs-Explorer** wird geöffnet.

    ![Menü des Aufgabenausführungs-Explorers](using-grunt/_static/task-runner-explorer-menu.png)

1. Stellen Sie sicher, dass `clean` unter **Aufgaben** im **Aufgabenausführungs-Explorer** angezeigt wird.

    ![Aufgabenliste des Aufgabenausführungs-Explorers](using-grunt/_static/task-runner-explorer-tasks.png)

1. Klicken Sie mit der rechten Maustaste auf die bereinigte Aufgabe, und wählen Sie **Ausführen** aus dem Kontextmenü aus. Ein Befehlsfenster zeigt den Fortschritt der Aufgabe an.

    ![Aufgabenausführungs-Explorer – Ausführen der bereinigten Aufgabe](using-grunt/_static/task-runner-explorer-run-clean.png)

    > [!NOTE]
    > Es sind noch keine Dateien oder Verzeichnisse zum Bereinigen vorhanden. Bei Bedarf können Sie diese manuell im Projektmappen-Explorer erstellen und dann die bereinigte Aufgabe als Test ausführen.

1. Fügen Sie in der Funktion `initConfig` einen Eintrag für `concat` mit dem folgenden Code ein.

    Das Eigenschaftenarray `src` listet die zu kombinierenden Dateien in der Reihenfolge auf, in der sie kombiniert werden sollen. Die Eigenschaft `dest` weist den Pfad zu der erzeugten kombinierten Datei zu.

    ```javascript
    concat: {
      all: {
        src: ['TypeScript/Tastes.js', 'TypeScript/Food.js'],
        dest: 'temp/combined.js'
      }
    },
    ```

    > [!NOTE]
    > Die Eigenschaft `all` im obigen Code ist der Name eines Ziels. In einigen Grunt-Aufgaben werden Ziele verwendet, um mehrere Buildumgebungen zu ermöglichen. Sie können die integrierten Ziele mit IntelliSense anzeigen oder eigene Ziele zuweisen.

1. Fügen Sie die Aufgabe `jshint` mit dem folgenden Code hinzu.

    Das Hilfsprogramm „jshint `code-quality`“ wird für jede JavaScript-Datei ausgeführt, die im Verzeichnis *temp* gefunden wird.

    ```javascript
    jshint: {
      files: ['temp/*.js'],
      options: {
        '-W069': false,
      }
    },
    ```

    > [!NOTE]
    > Die Option „-W069“ ist ein Fehler, der von jshint erzeugt wird, wenn JavaScript die Klammersyntax verwendet, um eine Eigenschaft anstelle der Punktnotation zuzuweisen, d. h. `Tastes["Sweet"]` anstelle von `Tastes.Sweet`. Die Option deaktiviert die Warnung, damit der Rest des Prozesses fortgesetzt werden kann.

1. Fügen Sie die Aufgabe `uglify` mit dem folgenden Code hinzu.

    Die Aufgabe verkleinert die Datei *combined.js* , die sich im Verzeichnis „temp“ befindet, und erstellt die Ergebnisdatei in „wwwroot/lib“ gemäß der Standardnamenskonvention *\<file name\>.min.js*.

    ```javascript
    uglify: {
     all: {
       src: ['temp/combined.js'],
       dest: 'wwwroot/lib/combined.min.js'
     }
    },
    ```

1. Fügen Sie unter dem Aufruf von `grunt.loadNpmTasks`, der `grunt-contrib-clean` lädt, den gleichen Aufruf für „jshint“, „concat“ und „uglify“ ein, indem Sie den folgenden Code verwenden.

    ```javascript
    grunt.loadNpmTasks('grunt-contrib-jshint');
    grunt.loadNpmTasks('grunt-contrib-concat');
    grunt.loadNpmTasks('grunt-contrib-uglify');
    ```

1. Speichern Sie *Grundfile.js*. Die Datei sollte in etwa so aussehen wie das Beispiel unten.

    ![Beispiel für eine vollständige Grunt-Datei](using-grunt/_static/gruntfile-js-complete.png)

1. Beachten Sie, dass die Aufgabenliste des **Aufgabenausführungs-Explorers** `clean`-, `concat`-, `jshint`- und `uglify`-Aufgaben enthält. Führen Sie jede Aufgabe der Reihe nach aus, und beobachten Sie die Ergebnisse im **Projektmappen-Explorer**. Jede Aufgabe sollte ohne Fehler ausgeführt werden.

    ![Aufgabenausführungs-Explorer – Ausführen der einzelnen Aufgaben](using-grunt/_static/task-runner-explorer-run-each-task.png)

    Die concat-Aufgabe erstellt eine neue *combined.js* -Datei und legt sie im Verzeichnis „temp“ ab. Die `jshint`-Aufgabe wird einfach ausgeführt und erzeugt keine Ausgabe. Die `uglify`-Aufgabe erstellt eine neue *combined.min.js* -Datei und platziert sie in *wwwroot/lib*. Nach der Fertigstellung sollte die Lösung in etwa so aussehen wie der Screenshot unten:

    ![Projektmappen-Explorer nach allen Aufgaben](using-grunt/_static/solution-explorer-after-all-tasks.png)

    > [!NOTE]
    > Weitere Informationen zu den Optionen für die einzelnen Pakete finden Sie unter [https://www.npmjs.com/](https://www.npmjs.com/), und suchen Sie den Paketnamen im Suchfeld auf der Hauptseite. Sie können z. B. das Paket grunt-contrib-clean suchen, um einen Dokumentationslink zu erhalten, der alle seine Parameter erläutert.

### <a name="all-together-now"></a>Zusammenfassung

Verwenden Sie die Grunt `registerTask()`-Methode, um eine Reihe von Aufgaben in einer bestimmten Reihenfolge auszuführen. Um z. B. die obigen Beispielschritte in der Reihenfolge clean -> concat -> jshint -> uglify auszuführen, fügen Sie den folgenden Code zum Modul hinzu. Der Code sollte auf derselben Ebene wie die loadNpmTasks()-Aufrufe außerhalb von initConfig hinzugefügt werden.

```javascript
grunt.registerTask("all", ['clean', 'concat', 'jshint', 'uglify']);
```

Die neue Aufgabe wird im Aufgabenausführungs-Explorer unter „Aliastasks“ angezeigt. Sie können sie mit der rechten Maustaste anklicken und genau wie andere Aufgaben ausführen. Die Aufgabe `all` führt `clean`, `concat`, `jshint` und `uglify` in dieser Reihenfolge aus.

![Alias Grunt-Aufgaben](using-grunt/_static/alias-tasks.png)

## <a name="watching-for-changes"></a>Erkennen von Änderungen

Eine `watch`-Aufgabe überwacht Dateien und Verzeichnisse. Das Überwachungselement löst automatisch Aufgaben aus, wenn es Änderungen feststellt. Fügen Sie den folgenden Code zu initConfig hinzu, um auf Änderungen an \*.js-Dateien im TypeScript-Verzeichnis zu achten. Wenn eine JavaScript-Datei geändert wird, führt `watch` die Aufgabe `all` aus.

```javascript
watch: {
  files: ["TypeScript/*.js"],
  tasks: ["all"]
}
```

Fügen Sie einen Aufruf an `loadNpmTasks()` hinzu, um die Aufgabe `watch` im Aufgabenausführungs-Explorer anzuzeigen.

```javascript
grunt.loadNpmTasks('grunt-contrib-watch');
```

Klicken Sie mit der rechten Maustaste auf das Überwachungselement im Aufgabenausführungs-Explorer, und wählen Sie im Kontextmenü „Ausführen“ aus. Im Befehlsfenster, das die aktive Überwachungsaufgabe anzeigt, wird die Meldung „Warten...“ angezeigt. Öffnen Sie eine der TypeScript-Dateien, fügen Sie ein Leerzeichen hinzu, und speichern Sie dann die Datei. Dadurch wird das Überwachungselement ausgelöst und die anderen Aufgaben werden der Reihe nach ausgeführt. Der folgende Screenshot zeigt eine Beispielausführung.

![Ausgabe aktiver Aufgaben](using-grunt/_static/watch-running.png)

## <a name="binding-to-visual-studio-events"></a>Binden an Visual Studio-Ereignisse

Wenn Sie Ihre Aufgaben nicht jedes Mal, wenn Sie in Visual Studio arbeiten, manuell starten möchten, binden Sie Aufgaben an die Ereignisse **Vor Build** , **Nach Build** , **Bereinigen** und **Projekt geöffnet**.

Binden Sie `watch`, sodass es bei jedem Öffnen von Visual Studio ausgeführt wird. Klicken Sie im Aufgabenausführungs-Explorer mit der rechten Maustaste auf das Überwachungselement, und wählen Sie **Bindungen** > **Projekt geöffnet** aus dem Kontextmenü aus.

![Binden einer Aufgabe an das Öffnen von Projekten](using-grunt/_static/bindings-project-open.png)

Entladen Sie das Projekt, und laden Sie es erneut. Wenn das Projekt wieder geladen wird, wird das Überwachungselement automatisch asugeführt.

## <a name="summary"></a>Zusammenfassung

Grunt ist eine leistungsstarke Aufgabenausführung, mit der sich die meisten Aufgaben für Clientbuilds automatisieren lassen. Grunt nutzt NPM zur Bereitstellung seiner Pakete und bietet eine Toolintegration mit Visual Studio. Der Aufgabenausführungs-Explorer von Visual Studio erkennt Änderungen an Konfigurationsdateien und bietet eine bequeme Schnittstelle zum Ausführen von Aufgaben, zum Anzeigen aktiver Aufgaben und zum Binden von Aufgaben an Visual Studio-Ereignisse.
