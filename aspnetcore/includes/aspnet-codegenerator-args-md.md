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
ms.openlocfilehash: 41d4fd2e746e08d32d9f666faab55acc56817be2
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551767"
---
<!-- Options common to Razor Pages and Controller -->
| Option               | BESCHREIBUNG|
| ----------------- | ------------ |
| --model oder -m  | Die Modellklasse, die verwendet werden soll. |
| --dataContext oder -dc  | Die `DbContext`-Klasse, die verwendet werden soll. |
| --bootstrapVersion oder -b  | Gibt die Bootstrap-Version an. Gültige Werte sind `3` und `4`. Der Standardwert ist `4`. Falls benötigt und noch nicht vorhanden, wird ein *wwwroot*-Verzeichnis erstellt, das die Bootstrap-Dateien der angegebenen Version enthält. |
| --referenceScriptLibraries oder -scripts |  Verweist auf Skriptbibliotheken in den erstellten Ansichten. Fügt `_ValidationScriptsPartial` zum Bearbeiten und Erstellen von Seiten hinzu. |
| --layout oder -l | Benutzerdefinierte Layoutseite, die verwendet werden soll. |
| --useDefaultLayout oder -udl | Verwendet das Standardlayout für die Ansichten. |
| --force oder -f | Überschreibt vorhandene Dateien. |
| --relativeFolderPath oder -outDir | Der relative Ausgabeordnerpfad des Projekts, in dem die Dateien erstellt werden. Wenn dieser nicht angegeben wird, werden die Dateien im Projektordner erstellt. |