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
ms.openlocfilehash: ed6de823b8b860c7d27e932e9ece40d8b43b0893
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551448"
---
Führen Sie das Identity Gerüst aus:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, >   >  **Neues Gerüst Element** hinzufügen.
* Wählen Sie im linken Bereich des Dialog Felds **Neues Gerüst Element hinzu** fügen die Option **Identity**  >  **Hinzufügen** aus.
* Wählen Sie im Dialogfeld **Hinzufügen Identity** die gewünschten Optionen aus.
  * Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben:
    * `~/Pages/Shared/_Layout.cshtml` für Razor Seiten
    * `~/Views/Shared/_Layout.cshtml` für MVC-Projekte
    * Blazor Server Apps, die aus der Blazor Server Vorlage () erstellt wurden, `blazorserver` sind nicht Razor standardmäßig für Seiten oder MVC konfiguriert. Lassen Sie den Eintrag Layoutseite leer.
  * Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse** zu erstellen. Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).
* Wählen Sie **Hinzufügen**.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Fügen Sie die erforderlichen nuget-Paket Verweise der Projektdatei (*. csproj*) hinzu. Führen Sie im Projektverzeichnis die folgenden Befehle aus:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

Führen Sie den folgenden Befehl aus, um die Identity Gerüst Optionen aufzulisten:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

Führen Sie im Projektordner das Identity Gerüst mit den gewünschten Optionen aus. Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten:

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
