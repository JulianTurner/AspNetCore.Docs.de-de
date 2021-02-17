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
<span data-ttu-id="57828-101">Der generierte Identity Datenbankcode erfordert [Entity Framework Core Migrationen](/ef/core/managing-schemas/migrations/).</span><span class="sxs-lookup"><span data-stu-id="57828-101">The generated Identity database code requires [Entity Framework Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="57828-102">Erstellen Sie eine Migration, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="57828-102">Create a migration and update the database.</span></span> <span data-ttu-id="57828-103">Führen Sie beispielsweise die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="57828-103">For example, run the following commands:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57828-104">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57828-104">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57828-105">In der Visual Studio- **Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="57828-105">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Add-Migration CreateIdentitySchema
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="57828-106">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="57828-106">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
```

---

<span data-ttu-id="57828-107">Der Name- Identity Parameter "Create Schema" für den `Add-Migration` Befehl ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="57828-107">The "CreateIdentitySchema" name parameter for the `Add-Migration` command is arbitrary.</span></span> <span data-ttu-id="57828-108">`"CreateIdentitySchema"` Beschreibt die Migration.</span><span class="sxs-lookup"><span data-stu-id="57828-108">`"CreateIdentitySchema"` describes the migration.</span></span>
