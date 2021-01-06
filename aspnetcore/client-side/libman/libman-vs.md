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
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>Verwenden von LibMan mit ASP.NET Core in Visual Studio

Von [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio verfügt über die integrierte Unterstützung für [LibMan](xref:client-side/libman/index) in ASP.NET Core-Projekten, einschließlich:

* Unterstützung für die Konfiguration und Ausführung von LibMan-Wiederherstellungvorgängen für den Build.
* Menüelemente zum Auslösen von LibMan-Wiederherstellungs- und -Bereinigungsvorgängen.
* Dialogfeld für die Suche nach Bibliotheken und zum Hinzufügen der Dateien zu einem Projekt.
* Unterstützung der Bearbeitung für *libman.json*&mdash;die LibMan-Manifestdatei.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**

## <a name="add-library-files"></a>Hinzufügen von Bibliotheksdateien

Bibliotheksdateien können einem ASP.NET Core-Projekt auf zwei verschiedene Arten hinzugefügt werden:

1. [Verwenden des Dialogfelds „Clientseitige Bibliothek hinzufügen“](#use-the-add-client-side-library-dialog)
1. [Manuelles Konfigurieren von LibMan-Manifestdateieinträgen](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Verwenden des Dialogfelds „Clientseitige Bibliothek hinzufügen“

Befolgen Sie diese Schritte, um eine clientseitige Bibliothek zu installieren:

* Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektordner, in dem die Dateien hinzugefügt werden sollen. Wählen Sie **Hinzufügen** > **Clientseitige Bibliothek** aus. Das Dialogfeld **Clientseitige Bibliothek hinzufügen** wird angezeigt:

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“](_static/add-library-dialog.png)

* Wählen Sie den Bibliotheksanbieter aus der Dropdownliste **Anbieter** aus. CDNJS ist der Standardanbieter.
* Geben Sie den Namen der abzurufenden Bibliothek in das Textfeld **Bibliothek** ein. IntelliSense stellt eine Liste von Bibliotheken zur Verfügung, die mit dem bereitgestellten Text beginnt.
* Wählen Sie die Bibliothek aus der IntelliSense-Liste aus. Beachten Sie, dass dem Bibliotheksnamen das Symbol `@` und die letzte stabile Version, die dem ausgewählten Anbieter bekannt ist, angefügt ist.
* Entscheiden Sie, welche Dateien einbezogen werden sollen:
  * Aktivieren Sie das Optionsfeld **Alle Bibliotheksdateien einbeziehen**, um alle Dateien der Bibliothek einzubeziehen.
  * Aktivieren Sie das Optionsfeld **Bestimmte Dateien auswählen**, um eine Teilmenge der Dateien der Bibliothek einzubeziehen. Wenn das Optionsfeld aktiviert ist, wird die Dateiauswahlstruktur aktiviert. Aktivieren Sie die Kästchen links neben den Dateinamen, die heruntergeladen werden sollen.
* Geben Sie den Projektordner für die Speicherung der Dateien im Textfeld **Zielspeicherort** an. Als Empfehlung sollten Sie jede Bibliothek in einem separaten Ordner speichern.

  Der vorgeschlagene Ordner **Zielspeicherort** basiert auf dem Speicherort, von dem aus das Dialogfeld gestartet wurde:

  * Beim Starten vom Projektstamm aus:
    * *wwwroot/lib* wird verwendet, wenn *wwwroot* vorhanden ist.
    * *lib* wird verwendet, wenn *wwwroot* nicht vorhanden ist.
  * Beim Starten von einem Projektordner aus, wird der entsprechende Ordnername verwendet.

  Dem Ordnervorschlag wird der Name der Bibliothek angefügt. In der folgenden Tabelle werden die Ordnervorschläge für die Installation von jQuery in einem Razor Pages-Projekt dargestellt.
  
  |Startposition                           |Vorgeschlagener Ordner      |
  |------------------------------------------|----------------------|
  |Projektstamm (wenn *wwwroot* vorhanden ist)        |*wwwroot/lib/jquery/* |
  |Projektstamm (wenn *wwwroot* nicht vorhanden ist) |*lib/jquery/*         |
  |*Pages*-Ordner im Projekt                 |*Pages/jquery/*       |

* Klicken Sie auf die Schaltfläche **Installieren**, um die Dateien gemäß der Konfiguration in *libman.json* herunterzuladen.
* Überprüfen Sie den **Bibliotheks-Manager**-Feed des Fensters **Ausgabe** auf Installationsdetails. Zum Beispiel:

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

### <a name="manually-configure-libman-manifest-file-entries"></a>Manuelles Konfigurieren von LibMan-Manifestdateieinträgen

Alle LibMan-Vorgänge in Visual Studio basieren auf dem Inhalt des LibMan-Manifests des Projektstamms (*libman.json*). Sie können *libman.json* manuell bearbeiten, um Bibliotheksdateien für das Projekt zu konfigurieren. Visual Studio stellt alle Bibliotheksdateien wieder her, sobald *libman.json* gespeichert wird.

Es gibt folgende Möglichkeiten, *libman.json* zur Bearbeitung zu öffnen:

* Doppelklicken Sie im **Projektmappen-Explorer** auf die Datei *libman.json*.
* Klicken Sie mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, und wählen Sie **Clientseitige Bibliotheken verwalten** aus. **&#8224;**
* Wählen Sie **Clientseitige Bibliotheken verwalten** im Visual Studio-Menü **Projekt** aus. **&#8224;**

**&#8224;** Wenn die Datei *libman.json* nicht bereits im Projektstamm vorhanden ist, wird sie mit dem Inhalt der Standardelementvorlage erstellt.

Visual Studio bietet umfangreiche Unterstützung für die JSON-Bearbeitung wie die farbliche Kennzeichnung, Formatierung, IntelliSense und Schemaüberprüfung. Das JSON-Schema des LibMan-Manifests befindet sich unter [https://json.schemastore.org/libman](https://json.schemastore.org/libman).

Mit der folgenden Manifestdatei ruft LibMan Dateien für die in der `libraries`-Eigenschaft definierte Konfiguration ab. Es folgt eine Erläuterung der in `libraries` definierten Objektliterale:

* Eine Teilmenge von [jQuery](https://jquery.com/) Version 3.3.1 wird vom CDNJS-Anbieter abgerufen. Die Teilmenge ist in der `files`-Eigenschaft&mdash;*jquery.min.js*, *jquery.js* und *jquery.min.map* definiert. Die Dateien werden im Ordner *wwwroot/lib/jquery* des Projekts abgelegt.
* [Bootstrap](https://getbootstrap.com/) Version 4.1.3 wird vollständig abgerufen und im Ordner *wwwroot/lib/bootstrap* abgelegt. Die Eigenschaft `provider` des Objektliterals setzt den Eigenschaftswert `defaultProvider` außer Kraft. LibMan ruft die Bootstrap-Dateien vom unpkg-Anbieter ab.
* Eine Teilmenge von [Lodash](https://lodash.com/) wurde von einer leitenden Instanz innerhalb der Organisation genehmigt. Die Dateien *lodash.js* und *lodash.min.js* werden aus dem lokalen Dateisystem unter *C:\\temp\\lodash\\* abgerufen. Die Dateien werden in den Ordner *wwwroot/lib/lodash* des Projekts kopiert.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan unterstützt nur eine Version jeder Bibliothek von den einzelnen Anbietern. Für die Datei *libman.json* tritt bei der Schemaüberprüfung ein Fehler auf, wenn sie zwei Bibliotheken mit demselben Bibliotheksnamen für einen bestimmten Anbieter enthält.

## <a name="restore-library-files"></a>Wiederherstellen von Bibliotheksdateien

Zum Wiederherstellen von Bibliotheksdateien in Visual Studio muss eine gültige *libman.json*-Datei im Projektstamm vorhanden sein. Wiederhergestellte Dateien werden im Projekt an dem für jede Bibliothek angegebenen Ort platziert.

Bibliotheksdateien können in einem ASP.NET Core-Projekt auf zwei Arten wiederhergestellt werden:

1. [Wiederherstellen von Dateien während der Erstellung](#restore-files-during-build)
1. [Manuelles Wiederherstellen von Dateien](#restore-files-manually)

### <a name="restore-files-during-build"></a>Wiederherstellen von Dateien während der Erstellung

LibMan kann die definierten Bibliotheksdateien im Rahmen des Buildprozesses wiederherstellen. Standardmäßig ist die *Wiederherstellung während des Builds* deaktiviert.

So aktivieren und testen Sie das Verhalten „Wiederherstellung während des Builds“

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf *libman.json*, und wählen Sie im Kontextmenü **Wiederherstellung clientseitiger Bibliotheken bei Builderstellung aktivieren** aus.
* Klicken Sie auf die Schaltfläche **Ja**, wenn Sie aufgefordert werden, ein NuGet-Paket zu installieren. Das NuGet-Paket [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) wird dem Projekt hinzugefügt:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Erstellen Sie das Projekt, um die Wiederherstellung der LibMan-Datei zu bestätigen. Das `Microsoft.Web.LibraryManager.Build`-Paket fügt ein MSBuild-Target ein, das LibMan während des Buildvorgangs des Projekts ausführt.
* Überprüfen Sie den **Build**-Feed des Fensters **Ausgabe** auf ein LibMan-Aktivitätsprotokoll:

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

Wenn das Verhalten „Wiederherstellung während des Builds“ aktiviert ist, zeigt das Kontextmenü *libman.json* die Option **Wiederherstellung clientseitiger Bibliotheken bei Builderstellung deaktivieren** an. Durch Auswahl dieser Option wird der Paketverweis `Microsoft.Web.LibraryManager.Build` aus der Projektdatei entfernt. Folglich werden die clientseitigen Bibliotheken nicht mehr bei jedem Build wiederhergestellt.

Unabhängig von der Einstellung „Wiederherstellung während des Builds“ können Sie die Wiederherstellung jederzeit manuell über das Kontextmenü *libman.json* durchführen. Weitere Informationen finden Sie unter [Manuelles Wiederherstellen von Dateien](#restore-files-manually).

### <a name="restore-files-manually"></a>Manuelles Wiederherstellen von Dateien

So stellen Sie Bibliotheksdateien manuell wieder her

* Gehen Sie für alle Projekte in der Lösung wie folgt vor:
  * Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Namen der Projektmappe.
  * Wählen Sie die Option **Wiederherstellung clientseitiger Bibliotheken bei Builderstellung aktivieren** aus.
* Gehen Sie für ein bestimmtes Projekt wie folgt vor:
  * Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Datei *libman.json*.
  * Wählen Sie die Option **Wiederherstellung clientseitiger Bibliotheken bei Builderstellung aktivieren** aus.

Gehen Sie wie folgt vor, während der Wiederherstellungsvorgang ausgeführt wird:

* Das Symbol für das Aufgabenstatuscenter in der Statusleiste von Visual Studio wird animiert und zeigt *Wiederherstellungsvorgang gestartet* an. Wenn Sie auf das Symbol klicken, wird eine QuickInfo mit den bekannten Hintergrundaufgaben geöffnet.
* Die Nachrichten werden an die Statusleiste und den **Bibliotheks-Manager**-Feed des Fensters **Ausgabe** gesendet. Zum Beispiel:

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

## <a name="delete-library-files"></a>Löschen von Bibliotheksdateien

So führen Sie den *Bereinigungsvorgang* aus, der zuvor in Visual Studio wiederhergestellte Bibliotheksdateien löscht

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Datei *libman.json*.
* Wählen Sie die Option **Clientseitige Bibliotheken bereinigen** aus.

Der Bereinigungsvorgang löscht keine vollständigen Verzeichnisse, um das unbeabsichtigte Entfernen von Nicht-Bibliotheksdateien zu verhindern. Es werden nur Dateien entfernt, die bei der vorherigen Wiederherstellung enthalten waren.

Während der Bereinigungsvorgang ausgeführt wird:

* Das Symbol für das Aufgabenstatuscenter in der Statusleiste von Visual Studio wird animiert und zeigt *Clientbibliotheksvorgang gestartet* an. Wenn Sie auf das Symbol klicken, wird eine QuickInfo mit den bekannten Hintergrundaufgaben geöffnet.
* Die Nachrichten werden an die Statusleiste und den **Bibliotheks-Manager**-Feed des Fensters **Ausgabe** gesendet. Zum Beispiel:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

Der Bereinigungsvorgang löscht nur Dateien aus dem Projekt. Bibliotheksdateien bleiben im Cache, um bei zukünftigen Wiederherstellungsvorgängen schneller abgerufen werden zu können. Verwenden Sie zur Verwaltung von Bibliotheksdateien, die im Cache des lokalen Computers gespeichert sind, die [LibMan-Befehlszeilenschnittstelle](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Deinstallieren von Bibliotheksdateien

So deinstallieren Sie Bibliotheksdateien

* Öffnen Sie *libman.json*.
* Positionieren Sie die Einfügemarke innerhalb des entsprechenden `libraries`-Objektliterals.
* Klicken Sie auf das Glühbirnensymbol, das am linken Rand angezeigt wird, und wählen Sie **Deinstallieren \<library_name>@\<library_version>** :

  ![Kontextmenüoption zum Deinstallieren der Bibliothek](_static/uninstall-menu-option.png)

Alternativ können Sie das LibMan-Manifest (*libman.json*) manuell bearbeiten und speichern. Der [Wiederherstellungsvorgang](#restore-library-files) wird beim Speichern der Datei ausgeführt. Bibliotheksdateien, die nicht mehr in *libman.json* definiert sind, werden aus dem Projekt entfernt.

## <a name="update-library-version"></a>Aktualisieren der Bibliotheksversion

So suchen Sie nach einer aktualisierten Bibliotheksversion

* Öffnen Sie *libman.json*.
* Positionieren Sie die Einfügemarke innerhalb des entsprechenden `libraries`-Objektliterals.
* Klicken Sie auf das Glühbirnensymbol, das am linken Rand angezeigt wird. Bewegen Sie den Mauszeiger über **Nach Updates suchen**.

LibMan sucht nach einer Bibliotheksversion, die neuer ist als die installierte Version. Die folgenden Ergebnisse können auftreten:

* Es wird die Nachricht **Keine Updates gefunden** angezeigt, wenn die aktuellste Version bereits installiert ist.
* Die aktuellste stabile Version wird angezeigt, falls sie nicht bereits installiert ist.

  ![Kontextmenüoption „Nach Updates suchen“](_static/update-menu-option.png)

* Wenn eine Vorabversion verfügbar ist, die aktueller ist als die installierte Version, wird die Vorabversion angezeigt.

Bearbeiten Sie die Datei *libman.json* manuell, um die Version auf eine ältere Bibliotheksversion herabzustufen. Wenn die Datei gespeichert wird, geht der LibMan-[Wiederherstellungsvorgang](#restore-library-files) wie folgt vor:

* Entfernt überflüssige Dateien aus der vorherigen Version.
* Fügt neue und aktualisierte Dateien aus der neuen Version hinzu.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:client-side/libman/libman-cli>
* [GitHub-Repository für LibMan](https://github.com/aspnet/LibraryManager)
