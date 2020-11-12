Führen Sie die folgenden .NET Core-CLI-Befehle aus:

```dotnetcli
dotnet tool install --global dotnet-ef --version 3.1.9
dotnet tool install --global dotnet-aspnet-codegenerator --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 3.1.9
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore --version 3.1.9
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 3.1.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 3.1.9
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 3.1.9
dotnet add package Microsoft.Extensions.Logging.Debug --version 3.1.9
```

Die oben aufgeführten Befehle fügen Folgendes hinzu:

* Das [Gerüstbautool „aspnet-codegenerator“](xref:fundamentals/tools/dotnet-aspnet-codegenerator).
* Die Entity Framework Core-Tools für die .NET Core-CLI.
* Den EF Core SQLite-Anbieter, der das EF Core-Paket als Abhängigkeit installiert.
* Für Gerüstbau erforderliche Pakete: `Microsoft.VisualStudio.Web.CodeGeneration.Design` und `Microsoft.EntityFrameworkCore.SqlServer`.

Eine Anleitung für die Konfiguration mehrerer Umgebungen, die es einer App ermöglicht, Ihre Datenbankkontexte nach Umgebung zu konfigurieren, finden Sie unter <xref:fundamentals/environments#environment-based-startup-class-and-methods>.

[!INCLUDE[](~/includes/scaffoldTFM.md)]