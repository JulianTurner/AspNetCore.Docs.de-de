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
ms.openlocfilehash: 1700adafb58cad57ea1becbf53cad45edd047962
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552889"
---
Der generierte Identity Datenbankcode erfordert [Entity Framework Core Migrationen](/ef/core/managing-schemas/migrations/). Erstellen Sie eine Migration, und aktualisieren Sie die Datenbank. Führen Sie beispielsweise die folgenden Befehle aus:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In der Visual Studio- **Paket-Manager-Konsole**:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

Der Name- Identity Parameter "Create Schema" für den `Add-Migration` Befehl ist willkürlich. `"CreateIdentitySchema"` Beschreibt die Migration.
