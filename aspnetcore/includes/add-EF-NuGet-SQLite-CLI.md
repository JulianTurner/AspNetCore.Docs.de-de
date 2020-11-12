<span data-ttu-id="1b440-101">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="1b440-101">Run the following .NET Core CLI commands:</span></span>

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

<span data-ttu-id="1b440-102">Die oben aufgeführten Befehle fügen Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="1b440-102">The preceding commands add:</span></span>

* <span data-ttu-id="1b440-103">Das [Gerüstbautool „aspnet-codegenerator“](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="1b440-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="1b440-104">Die Entity Framework Core-Tools für die .NET Core-CLI.</span><span class="sxs-lookup"><span data-stu-id="1b440-104">The Entity Framework Core Tools for the .NET Core CLI.</span></span>
* <span data-ttu-id="1b440-105">Den EF Core SQLite-Anbieter, der das EF Core-Paket als Abhängigkeit installiert.</span><span class="sxs-lookup"><span data-stu-id="1b440-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="1b440-106">Für Gerüstbau erforderliche Pakete: `Microsoft.VisualStudio.Web.CodeGeneration.Design` und `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="1b440-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="1b440-107">Eine Anleitung für die Konfiguration mehrerer Umgebungen, die es einer App ermöglicht, Ihre Datenbankkontexte nach Umgebung zu konfigurieren, finden Sie unter <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span><span class="sxs-lookup"><span data-stu-id="1b440-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM.md)]