---
title: 'Teil 4: Razor Pages mit EF Core in ASP.NET Core – Migrationen'
author: rick-anderson
description: Dies ist Teil 4 der Tutorialreihe zu Razor Pages und dem Entity Framework.
ms.author: riande
ms.date: 07/22/2019
no-loc:
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
uid: data/ef-rp/migrations
ms.openlocfilehash: 78eb466fcfeb130e411df490f033114b3fdebeef
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722630"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a>Teil 4: Razor Pages mit EF Core-Migrationen in ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra), [Jon P. Smith](https://twitter.com/thereformedprog) und [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

In diesem Tutorial wird das EF Core-Migrationsfeature für die Verwaltung von Datenmodelländerungen vorgestellt.

Wenn eine neue App entwickelt wird, ändert sich das Datenmodell häufig. Nach jeder Modelländerung ist das Modell nicht mehr synchron mit der Datenbank. In dieser Tutorialreihe wurde zunächst Entity Framework für die Erstellung der Datenbank konfiguriert, falls diese nicht vorhanden ist. Jedes Mal, wenn sich das Datenmodell ändert, müssen Sie die Datenbank löschen. Wenn die App das nächste Mal ausgeführt wird, wird durch den Aufruf von `EnsureCreated` die Datenbank erneut entsprechend dem neuen Datenmodell erstellt. Die `DbInitializer`-Klasse wird dann ausgeführt, um das Seeding der neuen Datenbank durchzuführen.

Dieser Ansatz, bei dem die Datenbank mit dem Datenmodell synchron gehalten wird, funktioniert gut, bis Sie die App für die Produktion bereitstellen. Wenn die App in der Produktionsumgebung ausgeführt wird, speichert sie in der Regel Daten, die verwaltet werden müssen. Wenn eine Änderung vorgenommen wird (z.B. eine neue Spalte hinzugefügt wird), kann die App nicht jedes Mal mit einer Testdatenbank starten. Mit dem EF Core-Migrationsfeature wird dieses Problem gelöst, indem EF Core das Datenbankschema aktualisiert, anstatt eine neue Datenbank zu erstellen.

Anstatt die Datenbank bei Änderungen des Datenmodell zu löschen und neu zu erstellen, erfolgt bei der Migration eine Aktualisierung des Schemas und die Beibehaltung vorhandener Daten.

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a>Löschen der Datenbank

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Verwenden Sie **SQL Server-Objekt-Explorer** (SSOX), um die Datenbank zu löschen, oder führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (PMC) aus:

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Führen Sie in der Eingabeaufforderung den folgenden Befehl aus, um die EF CLI zu installieren:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* Navigieren Sie in der Eingabeaufforderung zum Projektordner. Der Projektordner enthält die Datei *ContosoUniversity.csproj*.

* Löschen Sie die Datei *CU.db*, oder führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a>Erstellen einer ursprünglichen Migration

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Führen Sie die folgenden Befehle in der PMC aus:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stellen Sie sicher, dass sich die Eingabeaufforderung im Projektordner befindet, und führen Sie die folgenden Befehle aus:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a>Up- und Down-Methoden

Der EF Core-Befehl `migrations add` hat Code generiert, um die Datenbank zu erstellen. Dieser Migrationscode ist in der Datei *Migrations\<timestamp>_InitialCreate.cs* enthalten. Die Methode `Up` der Klasse `InitialCreate` erstellt die Datenbanktabellen, die den Datenmodell-Entitätenmengen entsprechen. Die Methode `Down` löscht diese, wie im folgenden Beispiel dargestellt:

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

Der vorangehende Code ist für die ursprüngliche Migration bestimmt. Der Code:

* Wurde durch den `migrations add InitialCreate`-Befehl generiert. 
* Wird durch den `database update`-Befehl ausgeführt.
* Erstellt eine Datenbank für das Datenmodell, das durch die Datenbankkontextklasse angegeben wird.

Der Parameter für den Migrationsnamen (in dem Beispiel „InitialCreate“) wird für den Dateinamen verwendet. Der Migrationsname kann ein beliebiger gültiger Dateiname sein. Es wird empfohlen, ein Wort oder einen Ausdruck auszuwählen, durch das bzw. den der Hintergrund der Migration widergespiegelt wird. Eine Migration, bei der eine Tabelle mit Fachbereichen hinzugefügt wurde, könnte beispielsweise als „AddDepartmentTable“ bezeichnet werden.

## <a name="the-migrations-history-table"></a>Die Migrationsverlaufstabelle

* Verwenden Sie SSOX oder das SQLite-Tool, um die Datenbank zu untersuchen.
* Beachten Sie die zusätzliche Tabelle`__EFMigrationsHistory`. In der Tabelle `__EFMigrationsHistory` wird nachverfolgt, welche Migrationen auf die Datenbank angewendet wurden.
* Zeigen Sie die Daten in der Tabelle `__EFMigrationsHistory` an. Es wird eine Zeile für die erste Migration angezeigt.

## <a name="the-data-model-snapshot"></a>Die Momentaufnahme des Datenmodells

Bei der Migration wird in *Migrations/SchoolContextModelSnapshot.cs* eine *Momentaufnahme* des aktuellen Datenmodells erstellt. Wenn Sie eine Migration hinzufügen, bestimmt EF die vorgenommenen Änderungen, indem das aktuelle Datenmodell mit der Momentaufnahmedatei verglichen wird.

Da die Momentaufnahmedatei den Status des Datenmodells nachverfolgt, können Sie eine Migration nicht löschen, indem Sie die Datei `<timestamp>_<migrationname>.cs` löschen. Zum Zurücksetzen der neuesten Migration müssen Sie den Befehl `migrations remove` verwenden. Dieser Befehl löscht die Migration und stellt sicher, dass die Momentaufnahme ordnungsgemäß zurückgesetzt wird. Weitere Informationen finden Sie unter [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

## <a name="remove-ensurecreated"></a>Entfernen von EnsureCreated

Diese Tutorialreihe hat mit der Verwendung von `EnsureCreated` begonnen. `EnsureCreated` erstellt keine Migrationsverlaufstabelle und kann daher nicht mit Migrationen verwendet werden. EnsureCreated dient zum Testen oder schnellen Erstellen von Prototypen, wenn die Datenbank häufig gelöscht und neu erstellt wird.

Ab diesem Zeitpunkt werden die Tutorials Migrationen verwenden.

Kommentieren Sie in *Data/DBInitializer.cs* die folgende Zeile aus:

```csharp
context.Database.EnsureCreated();
```
Führen Sie die App aus, und stellen Sie sicher, dass für die Datenbank ein Seeding durchgeführt wird.

## <a name="applying-migrations-in-production"></a>Anwenden von Migrationen in der Produktionsumgebung

Es wird empfohlen, dass Produktions-Apps [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) beim Anwendungsstart **nicht** aufrufen. `Migrate` sollte nicht aus einer APP aufgerufen werden, die in einer Serverfarm bereitgestellt wird. Wenn die App auf mehrere Serverinstanzen horizontal hochskaliert wird, ist es schwierig sicherzustellen, dass Datenbankschemaaktualisierungen nicht von mehreren Servern erfolgen oder mit Lese-/Schreibzugriffen einen Konflikt verursachen.

Die Datenbankmigration sollte im Rahmen der Bereitstellung und auf kontrollierte Weise erfolgen. Zu den Ansätzen für die Migration von Produktionsdatenbanken zählen die folgenden:

* Verwendung von Migrationen zur Erstellung von SQL-Skripts und Verwendung der SQL-Skripts bei der Bereitstellung.
* Ausführen von `dotnet ef database update` über eine kontrollierte Umgebung.

## <a name="troubleshooting"></a>Problembehandlung

Wenn die App SQL Server LocalDB verwendet und die folgende Ausnahme angezeigt wird:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Die Lösung kann die Ausführung von `dotnet ef database update` an einer Eingabeaufforderung sein.

### <a name="additional-resources"></a>Zusätzliche Ressourcen

* [EF Core-CLI](/ef/core/miscellaneous/cli/dotnet).
* [Paket-Manager-Konsole (Visual Studio)](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Tutorial wird das Datenmodell erstellt, und es werden Entitätseigenschaften und neue Entitäten hinzugefügt.

> [!div class="step-by-step"]
> [Vorheriges Tutorial](xref:data/ef-rp/sort-filter-page)
> [Nächstes Tutorial](xref:data/ef-rp/complex-data-model)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Tutorial wird das EF Core-Migrationsfeature zur Verwaltung von Datenmodelländerungen verwendet.

Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter.

Wenn eine neue App entwickelt wird, ändert sich das Datenmodell häufig. Nach jeder Modelländerung ist das Modell nicht mehr synchron mit der Datenbank. In diesem Tutorial wurde zunächst Entity Framework für die Erstellung der Datenbank konfiguriert, falls diese nicht vorhanden ist. Bei jeder Datenmodelländerung:

* Wird die Datenbank gelöscht.
* Erstellt EF eine neue, dem Modell entsprechende Datenbank.
* Füllt die App die Datenbank mit Testdaten auf.

Dieser Ansatz, bei dem die Datenbank mit dem Datenmodell synchron gehalten wird, funktioniert so lange, bis Sie die App für die Produktion bereitstellen. Wenn die App in der Produktionsumgebung ausgeführt wird, speichert sie in der Regel Daten, die verwaltet werden müssen. Jedes Mal, wenn eine Änderung vorgenommen wird (z.B. wenn eine neue Spalte hinzugefügt wird), kann die App nicht mit einer Testdatenbank starten. Mit der EF Core-Migrationsfeature wird dieses Problem gelöst, indem EF Core das Datenbankschema aktualisiert, anstatt eine neue Datenbank zu erstellen.

Statt die Datenbank bei den Datenmodelländerungen zu löschen und neu zu erstellen, wird bei der Migration eine Aktualisierung des Schemas und eine Beibehaltung vorhandener Daten angestrebt.

## <a name="drop-the-database"></a>Löschen der Datenbank

Verwenden Sie den **SQL Server-Objekt-Explorer** (SSOX) oder den Befehl `database drop`:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:

```powershell
Drop-Database
```

Führen Sie `Get-Help about_EntityFrameworkCore` über die Paket-Manager-Konsole aus, um Hilfeinformationen zu erhalten.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner. Der Projektordner enthält die Datei *Startup.cs*.

Geben Sie im Befehlsfenster Folgendes ein:

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a>Erstellen einer ursprünglichen Migration und Aktualisieren der Datenbank

Erstellen Sie das Projekt und die erste Migration.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a>Überprüfen der Methoden „Up“ und „Down“

Der EF Core-Befehl `migrations add` hat Code generiert, um die Datenbank zu erstellen. Dieser Migrationscode ist in der Datei *Migrations\<timestamp>_InitialCreate.cs* enthalten. Die Methode `Up` der Klasse `InitialCreate` erstellt die Datenbanktabellen, die den Datenmodellentitätenmengen entsprechen. Die Methode `Down` löscht diese, wie im folgenden Beispiel dargestellt:

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

Die Migrationsfunktion ruft die Methode `Up` auf, um die Datenmodelländerungen für eine Migration zu implementieren. Wenn Sie einen Befehl eingeben, um ein Rollback für das Update auszuführen, ruft die Migrationsfunktion die Methode `Down` auf.

Der vorangehende Code ist für die ursprüngliche Migration bestimmt. Dieser Code wurde bei der Ausführung des Befehls `migrations add InitialCreate` erstellt. Der Parameter für den Migrationsnamen (in dem Beispiel „InitialCreate“) wird für den Dateinamen verwendet. Der Migrationsname kann ein beliebiger gültiger Dateiname sein. Es wird empfohlen, ein Wort oder einen Ausdruck auszuwählen, durch das bzw. den der Hintergrund der Migration widergespiegelt wird. Eine Migration, bei der eine Tabelle mit Fachbereichen hinzugefügt wurde, könnte beispielsweise als „AddDepartmentTable“ bezeichnet werden.

Wenn die ursprüngliche Migration erstellt wird und die Datenbank vorhanden ist:

* Wird der Code für die Datenbankerstellung generiert.
* Muss der Code für die Datenbankerstellung nicht ausgeführt werden, da die Datenbank bereits mit dem Datenmodell übereinstimmt. Wenn der Code für die Datenbankerstellung ausgeführt wird, werden keine Änderungen vorgenommen, da die Datenbank bereits mit dem Datenmodell übereinstimmt.

Wenn die App in einer neuen Umgebung bereitgestellt wird, muss der Code für die Datenbankerstellung ausgeführt werden, damit die Datenbank erstellt wird.

Die Datenbank wurde zuvor gelöscht und ist daher nicht vorhanden, darum erstellt die Migration eine neue Datenbank.

### <a name="the-data-model-snapshot"></a>Die Momentaufnahme des Datenmodells

Die Migration erstellt unter *Migrations/SchoolContextModelSnapshot.cs* eine *Momentaufnahme* des aktuellen Datenbankschemas. Wenn Sie eine Migration hinzufügen, bestimmt EF die vorgenommenen Änderungen, indem das Datenmodell mit der Momentaufnahmedatei verglichen wird.

Verwenden Sie folgenden Befehl, um eine Migration zu löschen:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Remove-Migration

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

Weitere Informationen finden Sie unter [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).

---

Der Befehl „Remove-Migration“ löscht die Migration und stellt sicher, dass die Momentaufnahme ordnungsgemäß zurückgesetzt wird.

### <a name="remove-ensurecreated-and-test-the-app"></a>Entfernen von EnsureCreated und Testen der App

Zu einem frühen Entwicklungszeitpunkt wurde `EnsureCreated` verwendet. In diesem Tutorial werden Migrationen verwendet. Der Befehl `EnsureCreated` weist folgende Einschränkungen auf:

* Er umgeht Migrationen und erstellt die Datenbank und das Schema.
* Er erstellt keine Migrationstabelle.
* Er kann *nicht* mit Migrationen verwendet werden.
* Er dient zum Testen oder schnellen Erstellen von Prototypen an Positionen, an denen die Datenbank häufig gelöscht und neu erstellt wird.

Entfernen Sie `EnsureCreated`:

```csharp
context.Database.EnsureCreated();
```

Führen Sie die App aus, und stellen Sie sicher, dass für die Datenbank ein Seeding durchgeführt wird.

### <a name="inspect-the-database"></a>Untersuchen der Datenbank

Verwenden Sie den **SQL Server-Objekt-Explorer** zur Untersuchung der Datenbank. Beachten Sie die zusätzliche Tabelle`__EFMigrationsHistory`. In der Tabelle `__EFMigrationsHistory` wird nachverfolgt, welche Migrationen auf die Datenbank angewendet wurden. Wenn Sie die Daten in der Tabelle `__EFMigrationsHistory` anzeigen, wird eine Zeile für die erste Migration angezeigt. Im letzten Protokoll im vorherigen Beispiel der CLI-Ausgabe wird die INSERT-Anweisung angezeigt, mit der diese Zeile erstellt wird.

Führen Sie die App aus, und überprüfen Sie, ob alles funktioniert.

## <a name="applying-migrations-in-production"></a>Anwenden von Migrationen in der Produktionsumgebung

Es wird empfohlen, dass Produktions-Apps [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) beim Anwendungsstart **nicht** aufrufen. `Migrate` sollte in der Serverfarm nicht über eine App aufgerufen werden. Beispielsweise wenn die App über eine Cloud mit horizontaler Skalierung bereitgestellt wurde (mehrere Instanzen der App werden ausgeführt).

Die Datenbankmigration sollte im Rahmen der Bereitstellung und auf kontrollierte Weise erfolgen. Zu den Ansätzen für die Migration von Produktionsdatenbanken zählen die folgenden:

* Verwendung von Migrationen zur Erstellung von SQL-Skripts und Verwendung der SQL-Skripts bei der Bereitstellung.
* Ausführen von `dotnet ef database update` über eine kontrollierte Umgebung.

EF Core kann über die Tabelle `__MigrationsHistory` sehen, ob Migrationen ausgeführt werden müssen. Wenn die Datenbank auf dem neuesten Stand ist, wird keine Migration ausgeführt.

## <a name="troubleshooting"></a>Problembehandlung

Laden Sie die [fertige App](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations) herunter.

Die App generiert folgende Ausnahme:

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

Projektmappe: Ausführen von `dotnet ef database update`

### <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* [.NET Core-CLI](/ef/core/miscellaneous/cli/dotnet)
* [Paket-Manager-Konsole (Visual Studio)](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> [Zurück](xref:data/ef-rp/sort-filter-page)
> [Weiter](xref:data/ef-rp/complex-data-model)

::: moniker-end

