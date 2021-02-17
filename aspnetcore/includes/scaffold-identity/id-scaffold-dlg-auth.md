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

<span data-ttu-id="52bc7-101">Führen Sie das Identity Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="52bc7-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="52bc7-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52bc7-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="52bc7-103">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, >  > **Neues Gerüst Element** hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="52bc7-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="52bc7-104">Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identity** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="52bc7-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="52bc7-105">Wählen Sie im Dialogfeld **Hinzufügen Identity** die gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="52bc7-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="52bc7-106">Wählen Sie die vorhandene Layoutseite aus, sodass die Layoutdatei nicht mit einem falschen Markup überschrieben wird</span><span class="sxs-lookup"><span data-stu-id="52bc7-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="52bc7-107">Wenn eine vorhandene Datei " *\_ Layout. cshtml* " ausgewählt ist, wird Sie **nicht** überschrieben.</span><span class="sxs-lookup"><span data-stu-id="52bc7-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="52bc7-108">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="52bc7-108">For example:</span></span>
    * <span data-ttu-id="52bc7-109">`~/Pages/Shared/_Layout.cshtml` für Razor Seiten oder Blazor Server Projekte mit vorhandener Razor Seiten Infrastruktur</span><span class="sxs-lookup"><span data-stu-id="52bc7-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="52bc7-110">`~/Views/Shared/_Layout.cshtml` für MVC-Projekte oder- Blazor Server Projekte mit vorhandener MVC-Infrastruktur</span><span class="sxs-lookup"><span data-stu-id="52bc7-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="52bc7-111">Um den vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei aus, die Sie außer Kraft setzen möchten.</span><span class="sxs-lookup"><span data-stu-id="52bc7-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="52bc7-112">Sie müssen mindestens eine Datei auswählen, um den Datenkontext hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="52bc7-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="52bc7-113">Wählen Sie Ihre Datenkontext Klasse aus.</span><span class="sxs-lookup"><span data-stu-id="52bc7-113">Select your data context class.</span></span>
  * <span data-ttu-id="52bc7-114">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="52bc7-114">Select **Add**.</span></span>
* <span data-ttu-id="52bc7-115">So erstellen Sie einen neuen Benutzer Kontext und erstellen möglicherweise eine benutzerdefinierte Benutzerklasse für Identity :</span><span class="sxs-lookup"><span data-stu-id="52bc7-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="52bc7-116">Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse** zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="52bc7-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="52bc7-117">Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).</span><span class="sxs-lookup"><span data-stu-id="52bc7-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="52bc7-118">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="52bc7-118">Select **Add**.</span></span>

<span data-ttu-id="52bc7-119">Hinweis: Wenn Sie einen neuen Benutzer Kontext erstellen, müssen Sie keine Datei zum Überschreiben auswählen.</span><span class="sxs-lookup"><span data-stu-id="52bc7-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="52bc7-120">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="52bc7-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="52bc7-121">Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="52bc7-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="52bc7-122">Fügen Sie die erforderlichen nuget-Paket Verweise der Projektdatei (*. csproj*) hinzu.</span><span class="sxs-lookup"><span data-stu-id="52bc7-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="52bc7-123">Führen Sie im Projektverzeichnis die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="52bc7-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="52bc7-124">Führen Sie den folgenden Befehl aus, um die Identity Gerüst Optionen aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="52bc7-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="52bc7-125">Führen Sie im Projektordner das Identity Gerüst mit den gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="52bc7-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="52bc7-126">Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten.</span><span class="sxs-lookup"><span data-stu-id="52bc7-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="52bc7-127">Verwenden Sie den richtigen voll qualifizierten Namen für Ihren Daten Bank Kontext:</span><span class="sxs-lookup"><span data-stu-id="52bc7-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="52bc7-128">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="52bc7-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="52bc7-129">Wenn Sie PowerShell verwenden, versehen Sie die Semikolons in der Datei Liste, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein.</span><span class="sxs-lookup"><span data-stu-id="52bc7-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="52bc7-130">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="52bc7-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="52bc7-131">Wenn Sie das Identity gerüstup ausführen, ohne das `--files` Flag oder das `--useDefaultUI` Flag anzugeben, werden alle verfügbaren UI- Identity Seiten in Ihrem Projekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="52bc7-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="52bc7-132">Führen Sie das Identity Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="52bc7-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="52bc7-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="52bc7-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="52bc7-134">Klicken Sie in **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, >  > **Neues Gerüst Element** hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="52bc7-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="52bc7-135">Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identity** > **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="52bc7-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="52bc7-136">Wählen Sie im Dialogfeld **Hinzufügen Identity** die gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="52bc7-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="52bc7-137">Wählen Sie die vorhandene Layoutseite aus, oder die Layoutdatei wird mit einem falschen Markup überschrieben.</span><span class="sxs-lookup"><span data-stu-id="52bc7-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="52bc7-138">Wenn eine vorhandene Datei " *\_ Layout. cshtml* " ausgewählt ist, wird Sie **nicht** überschrieben.</span><span class="sxs-lookup"><span data-stu-id="52bc7-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="52bc7-139">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="52bc7-139">For example:</span></span>
    * <span data-ttu-id="52bc7-140">`~/Pages/Shared/_Layout.cshtml` für Razor Seiten</span><span class="sxs-lookup"><span data-stu-id="52bc7-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="52bc7-141">`~/Views/Shared/_Layout.cshtml` für MVC-Projekte</span><span class="sxs-lookup"><span data-stu-id="52bc7-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="52bc7-142">Um den vorhandenen Datenkontext zu verwenden, wählen Sie mindestens eine Datei aus, die Sie außer Kraft setzen möchten.</span><span class="sxs-lookup"><span data-stu-id="52bc7-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="52bc7-143">Sie müssen mindestens eine Datei auswählen, um den Datenkontext hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="52bc7-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="52bc7-144">Wählen Sie Ihre Datenkontext Klasse aus.</span><span class="sxs-lookup"><span data-stu-id="52bc7-144">Select your data context class.</span></span>
  * <span data-ttu-id="52bc7-145">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="52bc7-145">Select **Add**.</span></span>
* <span data-ttu-id="52bc7-146">So erstellen Sie einen neuen Benutzer Kontext und erstellen möglicherweise eine benutzerdefinierte Benutzerklasse für Identity :</span><span class="sxs-lookup"><span data-stu-id="52bc7-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="52bc7-147">Wählen Sie die **+** Schaltfläche aus, um eine neue **Datenkontext Klasse** zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="52bc7-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="52bc7-148">Übernehmen Sie den Standardwert, oder geben Sie eine Klasse an (z `MyApplication.Data.ApplicationDbContext` . b.).</span><span class="sxs-lookup"><span data-stu-id="52bc7-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="52bc7-149">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="52bc7-149">Select **Add**.</span></span>

<span data-ttu-id="52bc7-150">Hinweis: Wenn Sie einen neuen Benutzer Kontext erstellen, müssen Sie keine Datei zum Überschreiben auswählen.</span><span class="sxs-lookup"><span data-stu-id="52bc7-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="52bc7-151">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="52bc7-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="52bc7-152">Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="52bc7-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="52bc7-153">Fügen Sie der Projektdatei (*. csproj*) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="52bc7-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="52bc7-154">Führen Sie im Projektverzeichnis die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="52bc7-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="52bc7-155">Führen Sie den folgenden Befehl aus, um die Identity Gerüst Optionen aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="52bc7-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="52bc7-156">Führen Sie im Projektordner das Identity Gerüst mit den gewünschten Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="52bc7-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="52bc7-157">Führen Sie beispielsweise den folgenden Befehl aus, um die Identität mit der Standardbenutzer Oberfläche und der minimalen Anzahl von Dateien einzurichten.</span><span class="sxs-lookup"><span data-stu-id="52bc7-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="52bc7-158">Verwenden Sie den richtigen voll qualifizierten Namen für Ihren Daten Bank Kontext:</span><span class="sxs-lookup"><span data-stu-id="52bc7-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="52bc7-159">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="52bc7-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="52bc7-160">Wenn Sie PowerShell verwenden, versehen Sie die Semikolons in der Datei Liste, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein.</span><span class="sxs-lookup"><span data-stu-id="52bc7-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="52bc7-161">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="52bc7-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="52bc7-162">Wenn Sie das Identity gerüstup ausführen, ohne das `--files` Flag oder das `--useDefaultUI` Flag anzugeben, werden alle verfügbaren UI- Identity Seiten in Ihrem Projekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="52bc7-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
