---
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
ms.openlocfilehash: cf20722e8c8669fb17af8db032d4064ca2be2f4c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552244"
---
> [!NOTE]
> 
> **SQLite-Einschränkungen**
>
> Dieses Tutorial verwendet nach Möglichkeit das Entity Framework Core-Feature *Migrationen*. Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht. Bei Migrationen werden jedoch nur die Änderungen vorgenommen, die von der Datenbank-Engine unterstützt werden, und die Schemaänderungsfunktionen von SQLite sind beschränkt. Zum Beispiel wird Hinzufügen einer Spalten unterstützt, wogegen Entfernen einer Spalte nicht unterstützt wird. Wenn eine Migration zum Entfernen einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl. 
>
> Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert. Der Code würde die `Up`- und `Down`-Methoden für eine Migration verwenden und die folgenden Aufgaben ausführen:
>
> * Erstellen einer neuen Tabelle.
> * Kopieren von Daten aus der alten Tabelle in die neue Tabelle.
> * Löschen der alten Tabelle.
> * Umbenennen der neuen Tabelle.
>
> Das Schreiben von datenbankspezifischem Code dieses Typs übersteigt den Rahmen dieses Tutorials. Stattdessen wird die Datenbank in diesem Tutorial gelöscht und neu erstellt, wenn der Versuch fehlschlägt, eine Migration anzuwenden. Weitere Informationen finden Sie in den folgenden Ressourcen:
>
> * [SQLite EF Core-Datenbank-Anbieter-Einschränkungen](/ef/core/providers/sqlite/limitations)
> * [Anpassen des Migrationscodes](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Datenseeding](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE-Anweisung](https://sqlite.org/lang_altertable.html)