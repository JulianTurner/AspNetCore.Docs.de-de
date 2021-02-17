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
<span data-ttu-id="242be-101">Führen Sie das Identity Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="242be-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="242be-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="242be-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="242be-103">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, >   >  **Neues Gerüst Element** hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="242be-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="242be-104">Wählen Sie im linken Bereich des Dialog Felds **Neues Gerüst Element hinzu** fügen die Option **Identity**  >  **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="242be-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="242be-105">Wählen Sie im Dialogfeld **Hinzufügen Identity** die gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="242be-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="242be-106">Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben:</span><span class="sxs-lookup"><span data-stu-id="242be-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="242be-107">`~/Pages/Shared/_Layout.cshtml` für Razor Seiten</span><span class="sxs-lookup"><span data-stu-id="242be-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="242be-108">`~/Views/Shared/_Layout.cshtml` für MVC-Projekte</span><span class="sxs-lookup"><span data-stu-id="242be-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="242be-109">Blazor Server Apps, die aus der Blazor Server Vorlage () erstellt wurden, `blazorserver` sind nicht Razor standardmäßig für Seiten oder MVC konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="242be-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="242be-110">Lassen Sie den Eintrag Layoutseite leer.</span><span class="sxs-lookup"><span data-stu-id="242be-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="242be-111">Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse** zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="242be-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="242be-112">Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).</span><span class="sxs-lookup"><span data-stu-id="242be-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="242be-113">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="242be-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="242be-114">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="242be-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="242be-115">Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="242be-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="242be-116">Fügen Sie die erforderlichen nuget-Paket Verweise der Projektdatei (*. csproj*) hinzu.</span><span class="sxs-lookup"><span data-stu-id="242be-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="242be-117">Führen Sie im Projektverzeichnis die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="242be-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="242be-118">Führen Sie den folgenden Befehl aus, um die Identity Gerüst Optionen aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="242be-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="242be-119">Führen Sie im Projektordner das Identity Gerüst mit den gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="242be-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="242be-120">Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten:</span><span class="sxs-lookup"><span data-stu-id="242be-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
