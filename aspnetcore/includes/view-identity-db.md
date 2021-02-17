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
ms.openlocfilehash: ff0502f68546213d76fe33f45574b5d8654b522b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551575"
---
### <a name="view-the-identity-database"></a>Anzeigen der Identity Datenbank

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

* Wählen Sie im Menü **Ansicht** die Option **SQL Server-Objekt-Explorer** (ssox) aus.
* Navigieren Sie zu **(localdb) mssqllocaldb (SQL Server 13)**. Klicken Sie mit der rechten Maustaste auf **dbo. Aspnettusers**-  >  **Ansichts Daten**:

![Kontextmenü für die aspnettusers-Tabelle in SQL Server-Objekt-Explorer](~/security/authentication/accconfirm/_static/ssox.png)

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Es gibt zahlreiche Drittanbieter Tools, die Sie zum Verwalten und Anzeigen einer SQLite-Datenbank herunterladen können, z. b. [DB-Browser für SQLite](https://sqlitebrowser.org/).

---