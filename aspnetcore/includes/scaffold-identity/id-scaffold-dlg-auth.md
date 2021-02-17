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
ms.openlocfilehash: 1161f7731898221e51a4c7f9f246269b83c6ae80
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552658"
---
::: moniker range=">= aspnetcore-3.0"

Führen Sie das Identity Gerüst aus:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, >  > **Neues Gerüst Element** hinzufügen.
* Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identity** > **Hinzufügen** aus.
* Wählen Sie im Dialogfeld **Hinzufügen Identity** die gewünschten Optionen aus.
  * Wählen Sie die vorhandene Layoutseite aus, sodass die Layoutdatei nicht mit einem falschen Markup überschrieben wird Wenn eine vorhandene Datei " *\_ Layout. cshtml* " ausgewählt ist, wird Sie **nicht** überschrieben. Beispiel:
    * `~/Pages/Shared/_Layout.cshtml` für Razor Seiten oder Blazor Server Projekte mit vorhandener Razor Seiten Infrastruktur
    * `~/Views/Shared/_Layout.cshtml` für MVC-Projekte oder- Blazor Server Projekte mit vorhandener MVC-Infrastruktur
* Um den vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei aus, die Sie außer Kraft setzen möchten. Sie müssen mindestens eine Datei auswählen, um den Datenkontext hinzuzufügen.
  * Wählen Sie Ihre Datenkontext Klasse aus.
  * Wählen Sie **Hinzufügen**.
* So erstellen Sie einen neuen Benutzer Kontext und erstellen möglicherweise eine benutzerdefinierte Benutzerklasse für Identity :
  * Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse** zu erstellen. Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).
  * Wählen Sie **Hinzufügen**.

Hinweis: Wenn Sie einen neuen Benutzer Kontext erstellen, müssen Sie keine Datei zum Überschreiben auswählen.

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

Führen Sie im Projektordner das Identity Gerüst mit den gewünschten Optionen aus. Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten. Verwenden Sie den richtigen voll qualifizierten Namen für Ihren Daten Bank Kontext:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell verwendet ein Semikolon als Befehls Trennzeichen. Wenn Sie PowerShell verwenden, versehen Sie die Semikolons in der Datei Liste, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein. Beispiel:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Wenn Sie das Identity gerüstup ausführen, ohne das `--files` Flag oder das `--useDefaultUI` Flag anzugeben, werden alle verfügbaren UI- Identity Seiten in Ihrem Projekt erstellt.

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Führen Sie das Identity Gerüst aus:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, >  > **Neues Gerüst Element** hinzufügen.
* Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identity** > **Hinzufügen** aus.
* Wählen Sie im Dialogfeld **Hinzufügen Identity** die gewünschten Optionen aus.
  * Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben. Wenn eine vorhandene Datei " *\_ Layout. cshtml* " ausgewählt ist, wird Sie **nicht** überschrieben. Beispiel:
    * `~/Pages/Shared/_Layout.cshtml` für Razor Seiten
    * `~/Views/Shared/_Layout.cshtml` für MVC-Projekte
* Um den vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei aus, die Sie außer Kraft setzen möchten. Sie müssen mindestens eine Datei auswählen, um den Datenkontext hinzuzufügen.
  * Wählen Sie Ihre Datenkontext Klasse aus.
  * Wählen Sie **Hinzufügen**.
* So erstellen Sie einen neuen Benutzer Kontext und erstellen möglicherweise eine benutzerdefinierte Benutzerklasse für Identity :
  * Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse** zu erstellen. Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).
  * Wählen Sie **Hinzufügen**.

Hinweis: Wenn Sie einen neuen Benutzer Kontext erstellen, müssen Sie keine Datei zum Überschreiben auswählen.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Fügen Sie der Projektdatei (*. csproj*) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu. Führen Sie im Projektverzeichnis die folgenden Befehle aus:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Führen Sie den folgenden Befehl aus, um die Identity Gerüst Optionen aufzulisten:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Führen Sie im Projektordner das Identity Gerüst mit den gewünschten Optionen aus. Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten. Verwenden Sie den richtigen voll qualifizierten Namen für Ihren Daten Bank Kontext:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

PowerShell verwendet ein Semikolon als Befehls Trennzeichen. Wenn Sie PowerShell verwenden, versehen Sie die Semikolons in der Datei Liste, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein. Beispiel:

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

Wenn Sie das Identity gerüstup ausführen, ohne das `--files` Flag oder das `--useDefaultUI` Flag anzugeben, werden alle verfügbaren UI- Identity Seiten in Ihrem Projekt erstellt.

---

::: moniker-end
