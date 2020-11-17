<span data-ttu-id="bfba4-101">Führen Sie die folgenden .NET-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="bfba4-101">Run the following .NET CLI commands:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet add package Microsoft.EntityFrameworkCore.SQLite
dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.Extensions.Logging.Debug
```

<span data-ttu-id="bfba4-102">Die oben aufgeführten Befehle fügen Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="bfba4-102">The preceding commands add:</span></span>

* <span data-ttu-id="bfba4-103">Das [Gerüstbautool „aspnet-codegenerator“](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="bfba4-103">The [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>
* <span data-ttu-id="bfba4-104">Die EF Core-Tools für die .NET-CLI</span><span class="sxs-lookup"><span data-stu-id="bfba4-104">The EF Core Tools for the .NET CLI.</span></span>
* <span data-ttu-id="bfba4-105">Den EF Core SQLite-Anbieter, der das EF Core-Paket als Abhängigkeit installiert.</span><span class="sxs-lookup"><span data-stu-id="bfba4-105">The EF Core SQLite provider, which installs the EF Core package as a dependency.</span></span>
* <span data-ttu-id="bfba4-106">Für Gerüstbau erforderliche Pakete: `Microsoft.VisualStudio.Web.CodeGeneration.Design` und `Microsoft.EntityFrameworkCore.SqlServer`.</span><span class="sxs-lookup"><span data-stu-id="bfba4-106">Packages needed for scaffolding: `Microsoft.VisualStudio.Web.CodeGeneration.Design` and `Microsoft.EntityFrameworkCore.SqlServer`.</span></span>

<span data-ttu-id="bfba4-107">Eine Anleitung für die Konfiguration mehrerer Umgebungen, die es einer App ermöglicht, Ihre Datenbankkontexte nach Umgebung zu konfigurieren, finden Sie unter <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span><span class="sxs-lookup"><span data-stu-id="bfba4-107">For guidance on multiple environment configuration that permits an app to configure its database contexts by environment, see <xref:fundamentals/environments#environment-based-startup-class-and-methods>.</span></span>

[!INCLUDE[](~/includes/scaffoldTFM-5.md)]
