---
title: 'Teil 5 des Tutorials: Anwenden von Migrationen auf das Contoso University-Beispiel'
description: Teil 5 der Contoso University-Tutorialreihe Verwenden Sie das EF Core-Migrationsfeature für die Verwaltung von Datenmodelländerungen in einer ASP.NET Core MVC-App.
author: rick-anderson
ms.author: riande
ms.custom: contperf-fy21q2
ms.date: 11/13/2020
ms.topic: tutorial
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: 7c8f562bcf0b7e2672f2f1ac244e0d9278e4c204
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485926"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a>Tutorial: Teil 5, Anwenden von Migrationen auf das Contoso University-Beispiel

In diesem Tutorial verwenden Sie zunächst die EF Core-Migrationsfeature für die Verwaltung von Datenmodelländerungen. In späteren Tutorials fügen Sie weitere Migrationen hinzu, wenn Sie das Datenmodell ändern.

In diesem Tutorial:

> [!div class="checklist"]
> * Erfahren Sie mehr über Migrationen
> * Erstellen einer ursprünglichen Migration
> * Untersuchen Sie Up- und Down-Methoden
> * Erfahren Sie mehr über die Datenmodell-Momentaufnahme
> * Anwenden der Migration

## <a name="prerequisites"></a>Voraussetzungen

* [Sortieren, Filtern und Auslagern](sort-filter-page.md)

## <a name="about-migrations"></a>Informationen zu Migrationen

Wenn Sie eine neue Anwendung entwickeln, ändert sich Ihr Datenmodell häufig. Jedes Mal, wenn das Datenmodell geändert wird, ist es nicht mehr synchron mit der Datenbank. Sie haben zu Beginn dieser Tutorials Entity Framework für die Erstellung der Datenbank konfiguriert, sofern diese nicht vorhanden war. Anschließend können Sie bei jeder Datenmodelländerung (beim Hinzufügen, Entfernen oder Ändern von Entitätsklassen oder beim Ändern Ihrer DbContext-Klasse) die Datenbank löschen. EF erstellt dann eine neue Datenbank, die dem Modell entspricht, und startet diese mit Testdaten.

Diese Methode, bei der die Datenbank mit dem Datenmodell synchron gehalten wird, funktioniert so lange, bis Sie die Anwendung für die Produktion bereitstellen. Wenn sich die Anwendung in der Produktion befindet, speichert sie in der Regel die Daten, die Sie weiter verwenden möchten, da nicht bei jeder Änderung, wie z.B. dem Hinzufügen einer neuen Spalte, alle Daten verloren gehen sollen. Mit der EF Core-Migrationsfeature wird dieses Problem gelöst, indem EF das Datenbankschema aktualisiert, statt eine neue Datenbank zu erstellen.

Für die Arbeit mit Migrationen können Sie die **Paket-Manager-Konsole** (Package Manager Console, PMC) oder die CLI verwenden.  In diesen Tutorials wird die Verwendungsweise von CLI-Befehlen erläutert. Informationen zur PMC finden Sie am [Ende dieses Tutorials](#pmc).

## <a name="drop-the-database"></a>Löschen der Datenbank

Installieren Sie EF Core Tools als [globales Tool](/ef/core/miscellaneous/cli/dotnet), und löschen Sie die Datenbank:

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

Im folgenden Abschnitt wird erläutert, wie CLI-Befehle ausgeführt werden.

## <a name="create-an-initial-migration"></a>Erstellen einer ursprünglichen Migration

Speichern Sie Ihre Änderungen, und erstellen Sie das Projekt. Öffnen Sie anschließend ein Befehlsfenster, und navigieren Sie zu dem Projektordner. Sie können diesen Vorgang auf folgende Weise beschleunigen:

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie aus dem Kontextmenü die Option **Ordner in Datei-Explorer öffnen** aus.

  ![Menüelement „In Datei-Explorer öffnen“](migrations/_static/open-in-file-explorer.png)

* Geben Sie in die Adressleiste „cmd“ ein, und drücken Sie die EINGABETASTE.

  ![Öffnen des Befehlsfensters](migrations/_static/open-command-window.png)

Geben Sie im Befehlsfenster folgenden Befehl ein:

```dotnetcli
dotnet ef migrations add InitialCreate
```

Mit den obigen Befehlen wird eine ähnliche Ausgabe wie die folgende angezeigt:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

Wenn Ihnen die Fehlermeldung *Auf die Datei „ContosoUniversity.dll“ kann nicht zugegriffen werden, da sie von einem anderen Prozess verwendet wird.* angezeigt wird, suchen Sie in der Windows-Taskleiste nach dem Symbol „IIS Express“, klicken Sie mit der rechten Maustaste darauf, und klicken Sie anschließend auf **ContosoUniversity > Website beenden**.

## <a name="examine-up-and-down-methods"></a>Untersuchen Sie Up- und Down-Methoden

Wenn Sie den Befehl `migrations add` ausgeführt haben, hat EF den Code generiert, mit dem die Datenbank von Grund auf neu erstellt wird. Dieser Code befindet sich im Ordner *Migrationen* in der Datei *\<timestamp>_InitialCreate.cs*. Mit der Methode `Up` der `InitialCreate`-Klasse werden die Datenbanktabellen erstellt, die den Datenmodellentitäten entsprechen, und mit der Methode `Down` werden die Datenbanktabellen gelöscht (siehe folgendes Beispiel).

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Die Migrationsfunktion ruft die Methode `Up` auf, um die Datenmodelländerungen für eine Migration zu implementieren. Wenn Sie einen Befehl eingeben, um ein Rollback für das Update auszuführen, ruft die Migrationsfunktion die Methode `Down` auf.

Dieser Code ist für die ursprüngliche Migration vorgesehen, die bei der Eingabe des Befehls `migrations add InitialCreate` erstellt wurde. Der Parameter für den Migrationsnamen (in dem Beispiel „InitialCreate“) wird für den Dateinamen verwendet und kann einen beliebigen Namen haben. Es wird empfohlen, ein Wort oder einen Ausdruck auszuwählen, durch das bzw. den der Hintergrund der Migration widergespiegelt wird. Sie können einer späteren Migration beispielsweise den Namen „AddDepartmentTable“ geben.

Wenn Sie die ursprüngliche Migration erstellt haben, als die Datenbank bereits vorhanden war, wird der Code für die Datenbankerstellung zwar generiert, allerdings muss er nicht ausgeführt werden, da die Datenbank bereits dem Datenmodell entspricht. Wenn Sie die App in einer anderen Umgebung bereitstellen, in der die Datenbank noch nicht vorhanden ist, wird dieser Code ausgeführt, um Ihre Datenbank zu erstellen. Daher sollte dieser zunächst getestet werden. Aus diesem Grund haben Sie den Namen der Datenbank zuvor in der Verbindungszeichenfolge geändert – damit eine Datenbank bei Migrationen von Grund auf neu erstellt werden kann.

## <a name="the-data-model-snapshot"></a>Die Momentaufnahme des Datenmodells

Die Migrationsfunktion erstellt unter *Migrations/SchoolContextModelSnapshot.cs* eine *Momentaufnahme* des aktuellen Datenbankschemas. Wenn Sie eine Migration hinzufügen, bestimmt EF die vorgenommenen Änderungen, indem das Datenmodell mit der Momentaufnahmedatei verglichen wird.

Verwenden Sie den Befehl [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove), um eine Migration zu entfernen. `dotnet ef migrations remove` löscht die Migration und stellt sicher, dass die Momentaufnahme ordnungsgemäß zurückgesetzt wird. Wenn `dotnet ef migrations remove` fehlschlägt, verwenden Sie `dotnet ef migrations remove -v`, um weitere Informationen zu diesem Fehler zu erhalten.

Weitere Informationen dazu, wie die Momentaufnahmedatei verwendet wird, finden Sie unter [EF Core Migrations in Team Environments (EF Core-Migrationen in Teamumgebungen)](/ef/core/managing-schemas/migrations/teams).

## <a name="apply-the-migration"></a>Anwenden der Migration

Geben Sie folgenden Befehl in das Befehlsfenster ein, um die Datenbank mit den darin enthaltenen Tabellen zu erstellen.

```dotnetcli
dotnet ef database update
```

Die Ausgabe des Befehls ähnelt der des Befehls `migrations add`. Der Unterschied besteht darin, dass Ihnen Protokolle für die SQL-Befehle angezeigt werden, mit denen die Datenbank eingerichtet wird. In der folgenden Beispielausgabe werden die meisten Protokolle ausgelassen. Wenn diese Detailebene in Protokollnachrichten nicht angezeigt werden soll, können Sie die Protokollebene in der Datei *appsettings.Development.json* ändern. Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

Verwenden Sie den **SQL Server-Objekt-Explorer**, um die Datenbank wie im ersten Tutorial zu untersuchen.  Sie werden feststellen, dass die Tabelle „\_\_EFMigrationsHistory“ hinzugefügt wurde, in der nachverfolgt wird, welche Migrationen auf die Datenbank angewendet worden sind. Wenn Sie die Daten in dieser Tabelle anzeigen, ist eine Zeile für die erste Migration zu sehen. (Im letzten Protokoll im vorgehenden Beispiel der CLI-Ausgabe wird die INSERT-Anweisung angezeigt, mit der diese Zeile erstellt wird.)

Führen Sie die Anwendung aus, um zu überprüfen, ob alles wie gewohnt funktioniert.

![Indexseite „Studenten“](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>Vergleich von CLI und PMC

Die EF-Tools für die Verwaltung von Migrationen sind über .NET Core-CLI-Befehle oder über PowerShell-Cmdlets im Visual Studio-Fenster **Paket-Manager-Console** (PMC) verfügbar. In diesem Tutorial wird die Verwendungsweise der CLI erläutert. Sie können aber auch die PMC verwenden, wenn Sie diese bevorzugen.

Die EF-Befehle für die PMC-Befehle sind im Paket [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) enthalten. Dieses Paket ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten, weshalb Sie keinen Paketverweis hinzufügen müssen, wenn Ihre App über einen Paketverweis für `Microsoft.AspNetCore.App` verfügt.

**Wichtig:** Dieses Paket ist nicht mit dem Paket identisch, das Sie für die CLI durch Bearbeitung der *CSPROJ*-Datei installiert haben. Der Name dieses Pakets endet mit `Tools`, im Gegensatz zum Namen des CLI-Pakets, der mit `Tools.DotNet` endet.

Weitere Informationen zu CLI-Befehlen finden Sie unter [.NET Core-CLI](/ef/core/miscellaneous/cli/dotnet).

Weitere Informationen zu den PMC-Befehlen finden Sie unter [Paket-Manager-Konsole (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Abrufen des Codes

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a>Nächster Schritt

Fahren Sie mit dem nächsten Tutorial fort, um fortgeschrittenere Themen zum Erweitern des Datenmodells kennenzulernen. Dabei werden Sie weitere Migrationen erstellen und anwenden.

> [!div class="nextstepaction"]
> [ASP.NET Core MVC mit Entity Framework Core: Datenmodell (5 von 10)](complex-data-model.md)
