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
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054748"
---
# <a name="use-the-libman-cli-with-aspnet-core"></a>Verwenden der LibMan-CLI mit ASP.NET Core

Von [Scott Addie](https://twitter.com/Scott_Addie)

Die [LibMan](xref:client-side/libman/index)-CLI ist ein plattformübergreifendes Tool, das überall unterstützt wird, wo .NET Core unterstützt wird.

## <a name="prerequisites"></a>Voraussetzungen

* [!INCLUDE [2.1-SDK](../../includes/2.1-SDK.md)]

## <a name="installation"></a>Installation

Gehen Sie wie folgt vor, um die LibMan-CLI zu installieren:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli
```

Ein [globales .NET Core-Tool](/dotnet/core/tools/global-tools#install-a-global-tool) wird über das NuGet-Paket [Microsoft.Web.LibraryManager.Cli](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Cli/) installiert.

Gehen Sie wie folgt vor, um die LibMan-CLI aus einer bestimmten NuGet-Paketquelle zu installieren:

```dotnetcli
dotnet tool install -g Microsoft.Web.LibraryManager.Cli --version 1.0.94-g606058a278 --add-source C:\Temp\
```

Im obigen Beispiel wird mit der auf dem lokalen Windows-Computer gespeicherten Datei *C:\Temp\Microsoft.Web.LibraryManager.Cli.1.0.94-g606058a278.nupkg* ein globales .NET Core-Tool installiert.

## <a name="usage"></a>Verwendung

Nach der erfolgreichen Installation der CLI kann folgender Befehl verwendet werden:

```console
libman
```

Anzeigen der installierten Version der CLI:

```console
libman --version
```

Anzeigen der verfügbaren CLI-Befehle:

```console
libman --help
```

Mit dem obigen Befehl wird eine ähnliche Ausgabe wie die folgende angezeigt:

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

In den folgenden Abschnitten werden die verfügbaren CLI-Befehle erläutert.

## <a name="initialize-libman-in-the-project"></a>Initialisieren von LibMan im Projekt

Durch den Befehl `libman init` wird die Datei *libman.json* erstellt, sofern sie noch nicht existiert. Die erstellte Datei enthält den StandardiInhalt für Elementvorlagen.

### <a name="synopsis"></a>Übersicht

```console
libman init [-d|--default-destination] [-p|--default-provider] [--verbosity]
libman init [-h|--help]
```

### <a name="options"></a>Optionen

Für den Befehl `libman init` sind die folgenden Optionen verfügbar:

* `-d|--default-destination <PATH>`

  Mit dieser Option wird ein Pfad relativ zum aktuellen Ordner festgelegt. Bibliotheksdateien werden an diesem Ort installiert, wenn für eine Bibliothek die Eigenschaft `destination` in *libman.json* nicht definiert ist. Der Wert für `<PATH>` wird in die Eigenschaft `defaultDestination` in *libman.json* geschrieben.

* `-p|--default-provider <PROVIDER>`

  Mit dieser Option wird der Anbieter festgelegt, der verwendet werden soll, wenn für eine Bibliothek kein Anbieter definiert wurde. Der Wert für `<PROVIDER>` wird in die Eigenschaft `defaultProvider` in *libman.json* geschrieben. Ersetzen Sie `<PROVIDER>` durch einen der folgenden Werte:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Gehen Sie wie folgt vor, um die Datei *libman.json* in einem ASP.NET Core-Projekt zu erstellen:

* Navigieren Sie zum Projektstamm.
* Führen Sie den folgenden Befehl aus:

  ```console
  libman init
  ```

* Geben Sie den Namen des Standardanbieters ein, oder drücken Sie die EINGABETASTE (`Enter`), um CDNJS als Standardanbieter zu verwenden. Gültige Werte sind:

  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  ![Befehl „libman init“: Standardanbieter](_static/libman-init-provider.png)

Die Datei *libman.json* mit dem folgenden Inhalt wird dem Projektstamm hinzugefügt:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

## <a name="add-library-files"></a>Bibliotheksdateien hinzufügen

Mit dem Befehl `libman install` werden Bibliotheksdateien in das Projekt heruntergeladen und dort installiert. Die Datei *libman.json* wird hinzugefügt, sofern sie noch nicht existiert. Die Datei *libman.json* wird so geändert, dass dort Konfigurationsdetails für die Bibliotheksdateien gespeichert werden.

### <a name="synopsis"></a>Übersicht

```console
libman install <LIBRARY> [-d|--destination] [--files] [-p|--provider] [--verbosity]
libman install [-h|--help]
```

### <a name="arguments"></a>Argumente

`LIBRARY`

Hierbei handelt es sich um den Namen der zu installierenden Bibliothek. Dieser Name kann eine Notation der Versionsnummer (z. B. `@1.2.0`) enthalten.

### <a name="options"></a>Optionen

Für den Befehl `libman install` sind die folgenden Optionen verfügbar:

* `-d|--destination <PATH>`

  Mit dieser Option wird der Installationsort für die Bibliothek festgelegt. Wenn kein Ort angegeben wird, wird der Standardspeicherort verwendet. Wenn in *libman.json* kein Wert für die Eigenschaft `defaultDestination` festgelegt ist, ist diese Option erforderlich.

* `--files <FILE>`

  Geben Sie den Namen der zu installierenden Datei aus der Bibliothek an. Wenn kein Dateiname angegeben wird, werden alle Dateien aus der Bibliothek installiert. Geben Sie die Option `--files` für jede der zu installierenden Dateien separat an. Relative Pfade werden ebenfalls unterstützt. Beispiel: `--files dist/browser/signalr.js`.

* `-p|--provider <PROVIDER>`

  Mit dieser Option wird der Name des für den Erwerb der Bibliothek zu verwendenden Anbieters angegeben. Ersetzen Sie `<PROVIDER>` durch einen der folgenden Werte:
  
  [!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

  Wenn kein Wert festgelegt wird, wird der Wert der Eigenschaft `defaultProvider` in *libman.json* verwendet. Wenn in *libman.json* kein Wert für die Eigenschaft `defaultProvider` festgelegt ist, ist diese Option erforderlich.

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Sehen Sie sich das folgende Beispiel für die Datei *libman.json* an:

```json
{
  "version": "1.0",
  "defaultProvider": "cdnjs",
  "libraries": []
}
```

Installieren der Datei *jquery.min.js* der Version 3.2.1 von jQuery im Ordner *wwwroot/scripts/jquery* mit dem Anbieter CDNJS:

```console
libman install jquery@3.2.1 --provider cdnjs --destination wwwroot/scripts/jquery --files jquery.min.js
```

Die Datei *libman.json* ähnelt dem folgenden Beispiel:

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

Installieren der Dateien *calendar.js* und *calendar.css* aus *C:\\temp\\contosoCalendar\\* mit dem Dateisystemanbieter:

  ```console
  libman install C:\temp\contosoCalendar\ --provider filesystem --files calendar.js --files calendar.css
  ```

Es gibt zwei mögliche Gründe für die folgende Meldung:

* Die Datei *libman.json* enthält keinen Wert für die Eigenschaft `defaultDestination`.
* Die Option `-d|--destination` ist im Befehl `libman install` nicht enthalten.

![Befehl „libman install“: Ziel](_static/libman-install-destination.png)

Wenn das Standardziel bestätigt wurde, ähnelt die Datei *libman.json* dem folgenden Beispiel:

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

## <a name="restore-library-files"></a>Wiederherstellen von Bibliotheksdateien

Mit dem Befehl `libman restore` werden die in *libman.json* definierten Bibliotheksdateien installiert. Dabei gelten folgende Regeln:

* Wenn im Projektstamm keine Datei namens *libman.json* existiert, wird ein Fehler zurückgegeben.
* Wenn in einer Bibliothek ein Anbieter festgelegt ist, wird die Eigenschaft `defaultProvider` in *libman.json* ignoriert.
* Wenn in einer Bibliothek ein Ziel festgelegt ist, wird die Eigenschaft `defaultDestination` in *libman.json* ignoriert.

### <a name="synopsis"></a>Übersicht

```console
libman restore [--verbosity]
libman restore [-h|--help]
```

### <a name="options"></a>Optionen

Für den Befehl `libman restore` sind die folgenden Optionen verfügbar:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Wiederherstellen der in *libman.json* definierten Bibliotheksdateien:

```console
libman restore
```

## <a name="delete-library-files"></a>Löschen von Bibliotheksdateien

Mit dem Befehl `libman clean` werden zuvor mit LibMan wiederhergestellte Bibliotheksdateien gelöscht. Ordner, die nach diesem Vorgang leer sind, werden gelöscht. Die Konfigurationen für die Bibliotheksdateien in der Eigenschaft `libraries` in *libman.json* werden nicht entfernt.

### <a name="synopsis"></a>Übersicht

```console
libman clean [--verbosity]
libman clean [-h|--help]
```

### <a name="options"></a>Optionen

Für den Befehl `libman clean` sind die folgenden Optionen verfügbar:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Löschen von über LibMan installierten Bibliotheksdateien:

```console
libman clean
```

## <a name="uninstall-library-files"></a>Deinstallieren von Bibliotheksdateien

Mit dem Befehl `libman uninstall`:

* werden alle der angegebenen Bibliothek zugeordneten Dateien am in *libman.json* angegebenen Ziel gelöscht.
* wird die zugehörige Bibliothekskonfiguration in *libman.json* entfernt.

Ein Fehler tritt auf, wenn:

* im Projektstamm keine Datei namens *libman.json* existiert.
* die angegebene Bibliothek nicht existiert.

Wenn mehr als eine Bibliothek mit demselben Namen installiert ist, werden Sie dazu aufgefordert, eine auszuwählen.

### <a name="synopsis"></a>Übersicht

```console
libman uninstall <LIBRARY> [--verbosity]
libman uninstall [-h|--help]
```

### <a name="arguments"></a>Argumente

`LIBRARY`

Hierbei handelt es sich um den Namen der zu deinstallierenden Bibliothek. Dieser Name kann eine Notation der Versionsnummer (z. B. `@1.2.0`) enthalten.

### <a name="options"></a>Optionen

Für den Befehl `libman uninstall` sind die folgenden Optionen verfügbar:

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

Sehen Sie sich das folgende Beispiel für die Datei *libman.json* an:

[!code-json[](samples/LibManSample/libman.json)]

* jQuery kann mit einem der folgenden Befehle deinstalliert werden:

  ```console
  libman uninstall jquery
  ```

  ```console
  libman uninstall jquery@3.3.1
  ```

* Deinstallieren der über den Anbieter `filesystem` installierten Lodash-Dateien:

  ```console
  libman uninstall C:\temp\lodash\
  ```

## <a name="update-library-version"></a>Aktualisieren der Bibliotheksversion

Mit dem Befehl `libman update` wird eine über LibMan installierte Bibliothek auf die angegebene Version aktualisiert.

Ein Fehler tritt auf, wenn:

* im Projektstamm keine Datei namens *libman.json* existiert.
* die angegebene Bibliothek nicht existiert.

Wenn mehr als eine Bibliothek mit demselben Namen installiert ist, werden Sie dazu aufgefordert, eine auszuwählen.

### <a name="synopsis"></a>Übersicht

```console
libman update <LIBRARY> [-pre] [--to] [--verbosity]
libman update [-h|--help]
```

### <a name="arguments"></a>Argumente

`LIBRARY`

Hierbei handelt es sich um den Namen der zu aktualisierenden Bibliothek.

### <a name="options"></a>Optionen

Für den Befehl `libman update` sind die folgenden Optionen verfügbar:

* `-pre`

  Abrufen der aktuellen Vorabversion der Bibliothek

* `--to <VERSION>`

  Abrufen einer bestimmten Version der Bibliothek

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

* Aktualisieren von jQuery auf die aktuelle Version:

  ```console
  libman update jquery
  ```

* Aktualisieren von jQuery auf Version 3.3.1:

  ```console
  libman update jquery --to 3.3.1
  ```

* Aktualisieren von jQuery auf die aktuelle Vorabversion:

  ```console
  libman update jquery -pre
  ```

## <a name="manage-library-cache"></a>Bibliothekscache verwalten

Mit dem Befehl `libman cache` wird der LibMan-Bibliothekscache verwaltet. Der Anbieter `filesystem` verwendet den Bibliothekscache nicht.

### <a name="synopsis"></a>Übersicht

```console
libman cache clean [<PROVIDER>] [--verbosity]
libman cache list [--files] [--libraries] [--verbosity]
libman cache [-h|--help]
```

### <a name="arguments"></a>Argumente

`PROVIDER`

Dieses Argument wird nur mit dem Befehl `clean` verwendet. Damit wird der zu bereinigende Anbietercache angegeben. Gültige Werte sind:

[!INCLUDE [LibMan provider names](../../includes/libman-cli/provider-names.md)]

### <a name="options"></a>Optionen

Für den Befehl `libman cache` sind die folgenden Optionen verfügbar:

* `--files`

  Auflisten der zwischengespeicherten Dateien

* `--libraries`

  Auflisten der zwischengespeicherten Bibliotheken

[!INCLUDE [standard-cli-options](../../includes/libman-cli/standard-cli-options.md)]

### <a name="examples"></a>Beispiele

* Verwenden Sie einen der folgenden Befehle, um die Namen der zwischengespeicherten Bibliotheken nach Anbietern sortiert anzuzeigen:

  ```console
  libman cache list
  ```

  ```console
  libman cache list --libraries
  ```

  Dadurch werden Informationen angezeigt, die mit denen der folgenden Ausgabe vergleichbar sind:

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

* Anzeigen der Namen der zwischengespeicherten Bibliotheksdateien nach Anbietern sortiert:

  ```console
  libman cache list --files
  ```

  Dadurch werden Informationen angezeigt, die mit denen der folgenden Ausgabe vergleichbar sind:

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

  Beachten Sie, dass bei der obigen Ausgabe die Versionen 3.2.1 und 3.3.1 von jQuery unter dem Anbieter CDNJS zwischengespeichert sind.

* Leeren des Bibliothekscaches für den Anbieter CDNJS:

  ```console
  libman cache clean cdnjs
  ```

  Wenn der Cache für den Anbieter CDNJS geleert wurde, wird nach dem Ausführen des Befehls `libman cache list` Folgendes angezeigt:

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

* Leeren des Caches für alle unterstützten Anbieter:

  ```console
  libman cache clean
  ```

  Wenn alle Anbietercaches geleert wurden, wird nach dem Ausführen des Befehls `libman cache list` Folgendes angezeigt:

  ```console
  Cache contents:
  ---------------
  unpkg:
      (empty)
  cdnjs:
      (empty)
  ```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Installieren eines globalen Tools](/dotnet/core/tools/global-tools#install-a-global-tool)
* <xref:client-side/libman/libman-vs>
* [GitHub-Repository für LibMan](https://github.com/aspnet/LibraryManager)
