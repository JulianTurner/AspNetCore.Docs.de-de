---
title: Verwenden von ASP.NET Core SignalR mit Blazor
author: guardrex
description: Erstellen Sie eine Chat-App, die ASP.NET Core SignalR mit Blazor verwendet.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: f4e51b39c4c3b0c444b08025e9bd74eec0747541
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536373"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a><span data-ttu-id="ff268-103">Verwenden von ASP.NET Core SignalR mit Blazor</span><span class="sxs-lookup"><span data-stu-id="ff268-103">Use ASP.NET Core SignalR with Blazor</span></span>

<span data-ttu-id="ff268-104">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR mit Blazor beschrieben.</span><span class="sxs-lookup"><span data-stu-id="ff268-104">This tutorial teaches the basics of building a real-time app using SignalR with Blazor.</span></span> <span data-ttu-id="ff268-105">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="ff268-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ff268-106">Erstellen eines Blazor-Projekts</span><span class="sxs-lookup"><span data-stu-id="ff268-106">Create a Blazor project</span></span>
> * <span data-ttu-id="ff268-107">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="ff268-107">Add the SignalR client library</span></span>
> * <span data-ttu-id="ff268-108">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="ff268-108">Add a SignalR hub</span></span>
> * <span data-ttu-id="ff268-109">Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="ff268-109">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="ff268-110">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="ff268-110">Add Razor component code for chat</span></span>

<span data-ttu-id="ff268-111">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Chat-App.</span><span class="sxs-lookup"><span data-stu-id="ff268-111">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="ff268-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ff268-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ff268-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="ff268-113">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="ff268-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff268-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ff268-115">[Visual Studio 2019 Version 16.8 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="ff268-115">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff268-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff268-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff268-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff268-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="ff268-118">Visual Studio für Mac ab Version 8.8</span><span class="sxs-lookup"><span data-stu-id="ff268-118">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="ff268-119">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff268-119">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="ff268-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff268-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ff268-121">[Visual Studio 2019 Version 16.6 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="ff268-121">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff268-122">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff268-122">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff268-123">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff268-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="ff268-124">Visual Studio für Mac Version 8.6 oder höher</span><span class="sxs-lookup"><span data-stu-id="ff268-124">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="ff268-125">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff268-125">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="ff268-126">Erstellen einer gehosteten Blazor WebAssembly-App</span><span class="sxs-lookup"><span data-stu-id="ff268-126">Create a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="ff268-127">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="ff268-127">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff268-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff268-128">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ff268-129">Visual Studio 16.8 oder höher und das .NET Core SDK 5.0.0 oder höher sind erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ff268-129">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ff268-130">Visual Studio 16.6 oder höher und das .NET Core SDK 3.1.300 oder höher sind erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ff268-130">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="ff268-131">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="ff268-131">Create a new project.</span></span>

1. <span data-ttu-id="ff268-132">Klicken Sie auf **Blazor-App** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ff268-132">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="ff268-133">Geben Sie im Feld **Projektname** `BlazorWebAssemblySignalRApp` ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-133">Type `BlazorWebAssemblySignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="ff268-134">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="ff268-134">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ff268-135">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-135">Select **Create**.</span></span>

1. <span data-ttu-id="ff268-136">Wählen Sie die **Blazor WebAssembly App**-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-136">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="ff268-137">Aktivieren Sie unter **Erweitert** das Kontrollkästchen **Von ASP.net Core gehostet**.</span><span class="sxs-lookup"><span data-stu-id="ff268-137">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="ff268-138">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-138">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff268-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff268-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ff268-140">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   <span data-ttu-id="ff268-141">Mit der Option `-ho|--hosted` wird eine gehostete Blazor WebAssembly-Projektmappe erstellt.</span><span class="sxs-lookup"><span data-stu-id="ff268-141">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="ff268-142">Weitere Informationen zur Konfiguration von VS Code-Objekten im Ordner `.vscode` finden Sie im Leitfaden für **Linux**-Betriebssysteme unter <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="ff268-142">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

   <span data-ttu-id="ff268-143">Mit der Option `-o|--output` wird ein Ordner für die Projektmappe erstellt.</span><span class="sxs-lookup"><span data-stu-id="ff268-143">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="ff268-144">Wenn Sie einen Ordner für die Projektmappe erstellt haben und dieser Ordner in der Befehlsshell geöffnet ist, lassen Sie zum Erstellen der Projektmappe die Option `-o|--output` und den entsprechenden Wert aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-144">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

1. <span data-ttu-id="ff268-145">Öffnen Sie in Visual Studio Code den Projektordner der App.</span><span class="sxs-lookup"><span data-stu-id="ff268-145">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="ff268-146">Wenn das Dialogfeld angezeigt wird, um Assets zum Erstellen und Debuggen der App hinzuzufügen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-146">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="ff268-147">Visual Studio Code fügt den `.vscode`-Ordner automatisch mit generierten `launch.json`- und `tasks.json`-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff268-147">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff268-148">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff268-148">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff268-149">Installieren Sie die neueste Version von [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/), und führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-149">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="ff268-150">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="ff268-150">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="ff268-151">Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="ff268-151">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="ff268-152">Wählen Sie die **Blazor WebAssembly App**-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-152">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="ff268-153">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ff268-153">Select **Next**.</span></span>

1. <span data-ttu-id="ff268-154">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="ff268-154">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="ff268-155">Aktivieren Sie das Kontrollkästchen **ASP.NET Core Hosted** (Auf ASP.NET Core gehostet).</span><span class="sxs-lookup"><span data-stu-id="ff268-155">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="ff268-156">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ff268-156">Select **Next**.</span></span>

1. <span data-ttu-id="ff268-157">Benennen Sie im Feld **Projektname** die App `BlazorWebAssemblySignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="ff268-157">In the **Project Name** field, name the app `BlazorWebAssemblySignalRApp`.</span></span> <span data-ttu-id="ff268-158">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-158">Select **Create**.</span></span>

   <span data-ttu-id="ff268-159">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="ff268-159">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="ff268-160">Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="ff268-160">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="ff268-161">Öffnen Sie das Projekt, indem Sie zum Projektordner navigieren und die Projektmappendatei des Projekts (`.sln`) öffnen.</span><span class="sxs-lookup"><span data-stu-id="ff268-161">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff268-162">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff268-162">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff268-163">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-163">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

<span data-ttu-id="ff268-164">Mit der Option `-ho|--hosted` wird eine gehostete Blazor WebAssembly-Projektmappe erstellt.</span><span class="sxs-lookup"><span data-stu-id="ff268-164">The `-ho|--hosted` option creates a hosted Blazor WebAssembly solution.</span></span>

<span data-ttu-id="ff268-165">Mit der Option `-o|--output` wird ein Ordner für die Projektmappe erstellt.</span><span class="sxs-lookup"><span data-stu-id="ff268-165">The `-o|--output` option creates a folder for the solution.</span></span> <span data-ttu-id="ff268-166">Wenn Sie einen Ordner für die Projektmappe erstellt haben und dieser Ordner in der Befehlsshell geöffnet ist, lassen Sie zum Erstellen der Projektmappe die Option `-o|--output` und den entsprechenden Wert aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-166">If you've created a folder for the solution and the command shell is open in that folder, omit the `-o|--output` option and value to create the solution.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="ff268-167">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="ff268-167">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff268-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff268-168">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="ff268-169">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorWebAssemblySignalRApp.Client`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-169">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff268-170">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf `nuget.org` eingestellt ist.</span><span class="sxs-lookup"><span data-stu-id="ff268-170">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff268-171">Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-171">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="ff268-172">Wählen Sie in den Suchergebnissen das Paket [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-172">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="ff268-173">Legen Sie die Version so fest, dass sie zum freigegebenen Framework der App passt.</span><span class="sxs-lookup"><span data-stu-id="ff268-173">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="ff268-174">Wählen Sie **Installieren** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-174">Select **Install**.</span></span>

1. <span data-ttu-id="ff268-175">Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-175">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="ff268-176">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="ff268-176">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff268-177">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff268-177">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="ff268-178">Führen Sie im **integrierten Terminal** (**Ansicht** > **Terminal** auf der Symbolleiste) den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-178">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="ff268-179">Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ff268-179">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff268-180">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff268-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff268-181">Klicken Sie in der **Projektmappen**-Randleiste mit der rechten Maustaste auf das `BlazorWebAssemblySignalRApp.Client`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-181">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff268-182">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="ff268-182">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff268-183">Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-183">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="ff268-184">Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem Paket [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).</span><span class="sxs-lookup"><span data-stu-id="ff268-184">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="ff268-185">Legen Sie die Version so fest, dass sie zum freigegebenen Framework der App passt.</span><span class="sxs-lookup"><span data-stu-id="ff268-185">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="ff268-186">Klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="ff268-186">Select **Add Package**.</span></span>

1. <span data-ttu-id="ff268-187">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="ff268-187">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff268-188">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff268-188">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff268-189">Führen Sie in einer Befehlsshell im Ordner für die Projektmappe den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-189">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="ff268-190">Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ff268-190">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="ff268-191">Hinzufügen des System.Text.Encodings.Web-Pakets</span><span class="sxs-lookup"><span data-stu-id="ff268-191">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="ff268-192">*Dieser Abschnitt gilt nur für Apps für ASP.NET Core Version 3.x.*</span><span class="sxs-lookup"><span data-stu-id="ff268-192">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="ff268-193">Aufgrund eines Problems bei der Paketauflösung bei der Verwendung von [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in einer ASP.NET Core 3.x-App erfordert das `BlazorWebAssemblySignalRApp.Server`-Projekt einen Paketverweis für [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="ff268-193">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the `BlazorWebAssemblySignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="ff268-194">Das zugrunde liegende Problem wird in einem zukünftigen Patchrelease von .NET 5 gelöst.</span><span class="sxs-lookup"><span data-stu-id="ff268-194">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="ff268-195">Weitere Informationen finden Sie unter [System.Text.Json defines netcoreapp3.0 with no dependencies #45560](https://github.com/dotnet/runtime/issues/45560) (System.Text.Json definiert netcoreapp3.0 ohne Abhängigkeiten – Nr.45560).</span><span class="sxs-lookup"><span data-stu-id="ff268-195">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="ff268-196">Um [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) dem `BlazorWebAssemblySignalRApp.Server`-Projekt der unter ASP.NET Core 3.1 gehosteten Blazor-Lösung hinzuzufügen, befolgen Sie die Anleitungen für Ihre Toolsauswahl:</span><span class="sxs-lookup"><span data-stu-id="ff268-196">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorWebAssemblySignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff268-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff268-197">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="ff268-198">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorWebAssemblySignalRApp.Server`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-198">In **Solution Explorer**, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff268-199">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf `nuget.org` eingestellt ist.</span><span class="sxs-lookup"><span data-stu-id="ff268-199">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff268-200">Geben Sie bei Auswahl von **Durchsuchen** `System.Text.Encodings.Web` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-200">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="ff268-201">Wählen Sie in den Suchergebnissen das Paket [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-201">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="ff268-202">Wählen Sie die Version des Pakets aus, die zum verwendeten freigegebenen Framework passt.</span><span class="sxs-lookup"><span data-stu-id="ff268-202">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="ff268-203">Wählen Sie **Installieren** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-203">Select **Install**.</span></span>

1. <span data-ttu-id="ff268-204">Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-204">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="ff268-205">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="ff268-205">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff268-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff268-206">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="ff268-207">Führen Sie im **integrierten Terminal** (**Ansicht**>**Terminal** in der Symbolleiste) die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-207">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="ff268-208">Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ff268-208">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff268-209">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff268-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff268-210">Klicken Sie in der **Projektmappen**-Randleiste mit der rechten Maustaste auf das `BlazorWebAssemblySignalRApp.Server`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-210">In the **Solution** sidebar, right-click the `BlazorWebAssemblySignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff268-211">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="ff268-211">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff268-212">Geben Sie bei Auswahl von **Durchsuchen** `System.Text.Encodings.Web` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-212">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="ff268-213">Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem Paket [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web), wählen Sie die richtige Version des Pakets für das verwendete freigegebene Framework aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="ff268-213">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="ff268-214">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="ff268-214">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff268-215">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff268-215">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff268-216">Führen Sie in einer Befehlsshell im Ordner für die Projektmappe den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-216">In a command shell from the solution's folder, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

<span data-ttu-id="ff268-217">Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ff268-217">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="ff268-218">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="ff268-218">Add a SignalR hub</span></span>

<span data-ttu-id="ff268-219">Erstellen Sie im `BlazorWebAssemblySignalRApp.Server`-Projekt einen `Hubs`-Ordner (Plural), und fügen Sie die folgende `ChatHub`-Klasse (`Hubs/ChatHub.cs`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="ff268-219">In the `BlazorWebAssemblySignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="ff268-220">Hinzufügen von Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="ff268-220">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="ff268-221">Öffnen Sie im Projekt `BlazorWebAssemblySignalRApp.Server` die Datei `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="ff268-221">In the `BlazorWebAssemblySignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="ff268-222">Fügen Sie am Anfang der Datei den Namespace für die `ChatHub`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="ff268-222">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ff268-223">Fügen Sie SignalR und Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ff268-223">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. <span data-ttu-id="ff268-224">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff268-224">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="ff268-225">Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.</span><span class="sxs-lookup"><span data-stu-id="ff268-225">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="ff268-226">Fügen Sie zwischen den Endpunkten für Controller und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff268-226">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ff268-227">Fügen Sie SignalR und Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ff268-227">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="ff268-228">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff268-228">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="ff268-229">Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.</span><span class="sxs-lookup"><span data-stu-id="ff268-229">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="ff268-230">Fügen Sie zwischen den Endpunkten für Controller und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff268-230">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="ff268-231">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="ff268-231">Add Razor component code for chat</span></span>

1. <span data-ttu-id="ff268-232">Öffnen Sie im Projekt `BlazorWebAssemblySignalRApp.Client` die Datei `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="ff268-232">In the `BlazorWebAssemblySignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ff268-233">Ersetzen Sie das Markup durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ff268-233">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ff268-234">Ersetzen Sie das Markup durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ff268-234">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="ff268-235">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="ff268-235">Run the app</span></span>

<span data-ttu-id="ff268-236">Befolgen Sie die Anleitungen für die Auswahl Ihrer Tools:</span><span class="sxs-lookup"><span data-stu-id="ff268-236">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff268-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff268-237">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ff268-238">Wählen Sie im **Projektmappen-Explorer** das `BlazorWebAssemblySignalRApp.Server`-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-238">In **Solution Explorer**, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="ff268-239">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff268-239">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff268-240">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff268-241">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="ff268-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff268-242">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="ff268-242">The name and message are displayed on both pages instantly:</span></span>

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff268-244">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff268-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff268-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff268-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ff268-246">Weitere Informationen zur Konfiguration von VS Code-Objekten im Ordner `.vscode` finden Sie im Leitfaden für **Linux**-Betriebssysteme unter <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="ff268-246">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

1. <span data-ttu-id="ff268-247">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff268-247">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff268-248">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-248">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff268-249">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="ff268-249">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff268-250">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="ff268-250">The name and message are displayed on both pages instantly:</span></span>

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff268-252">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff268-252">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff268-253">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff268-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff268-254">Wählen Sie in der **Projektmappen**-Randleiste das `BlazorWebAssemblySignalRApp.Server`-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-254">In the **Solution** sidebar, select the `BlazorWebAssemblySignalRApp.Server` project.</span></span> <span data-ttu-id="ff268-255">Drücken Sie <kbd>⌘</kbd>+<kbd>↩</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff268-255">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff268-256">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-256">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff268-257">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="ff268-257">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff268-258">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="ff268-258">The name and message are displayed on both pages instantly:</span></span>

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff268-260">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff268-260">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff268-261">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff268-261">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ff268-262">Führen Sie in einer Befehlsshell im Ordner für die Projektmappe die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-262">In a command shell from the solution's folder, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="ff268-263">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-263">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff268-264">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="ff268-264">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff268-265">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="ff268-265">The name and message are displayed on both pages instantly:</span></span>

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff268-267">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff268-267">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a><span data-ttu-id="ff268-268">Erstellen einer Blazor Server-App</span><span class="sxs-lookup"><span data-stu-id="ff268-268">Create a Blazor Server app</span></span>

<span data-ttu-id="ff268-269">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="ff268-269">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff268-270">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff268-270">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ff268-271">Visual Studio 16.8 oder höher und das .NET Core SDK 5.0.0 oder höher sind erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ff268-271">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ff268-272">Visual Studio 16.6 oder höher und das .NET Core SDK 3.1.300 oder höher sind erforderlich.</span><span class="sxs-lookup"><span data-stu-id="ff268-272">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="ff268-273">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="ff268-273">Create a new project.</span></span>

1. <span data-ttu-id="ff268-274">Klicken Sie auf **Blazor-App** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ff268-274">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="ff268-275">Geben Sie im Feld **Projektname** `BlazorServerSignalRApp` ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-275">Type `BlazorServerSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="ff268-276">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="ff268-276">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ff268-277">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-277">Select **Create**.</span></span>

1. <span data-ttu-id="ff268-278">Wählen Sie die **Blazor Server App**-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-278">Choose the **Blazor Server App** template.</span></span>

1. <span data-ttu-id="ff268-279">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-279">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff268-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff268-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ff268-281">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-281">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   <span data-ttu-id="ff268-282">Mit der Option `-o|--output` wird ein Ordner für das Projekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="ff268-282">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="ff268-283">Wenn Sie einen Ordner für das Projekt erstellt haben und dieser Ordner in der Befehlsshell geöffnet ist, lassen Sie zum Erstellen des Projekts die Option `-o|--output` und den entsprechenden Wert aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-283">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

1. <span data-ttu-id="ff268-284">Öffnen Sie in Visual Studio Code den Projektordner der App.</span><span class="sxs-lookup"><span data-stu-id="ff268-284">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="ff268-285">Wenn das Dialogfeld angezeigt wird, um Assets zum Erstellen und Debuggen der App hinzuzufügen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-285">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="ff268-286">Visual Studio Code fügt den `.vscode`-Ordner automatisch mit generierten `launch.json`- und `tasks.json`-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff268-286">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span> <span data-ttu-id="ff268-287">Weitere Informationen zur Konfiguration von VS Code-Objekten im Ordner `.vscode` finden Sie im Leitfaden für **Linux**-Betriebssysteme unter <xref:blazor/tooling>.</span><span class="sxs-lookup"><span data-stu-id="ff268-287">For information on configuring VS Code assets in the `.vscode` folder, see the **Linux** operating system guidance in <xref:blazor/tooling>.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff268-288">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff268-288">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff268-289">Installieren Sie die neueste Version von [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/), und führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-289">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="ff268-290">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="ff268-290">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="ff268-291">Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="ff268-291">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="ff268-292">Wählen Sie die **Blazor Server App**-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-292">Choose the **Blazor Server App** template.</span></span> <span data-ttu-id="ff268-293">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ff268-293">Select **Next**.</span></span>

1. <span data-ttu-id="ff268-294">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="ff268-294">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="ff268-295">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ff268-295">Select **Next**.</span></span>

1. <span data-ttu-id="ff268-296">Benennen Sie im Feld **Projektname** die App `BlazorServerSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="ff268-296">In the **Project Name** field, name the app `BlazorServerSignalRApp`.</span></span> <span data-ttu-id="ff268-297">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-297">Select **Create**.</span></span>

   <span data-ttu-id="ff268-298">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="ff268-298">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="ff268-299">Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="ff268-299">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="ff268-300">Öffnen Sie das Projekt, indem Sie zum Projektordner navigieren und die Projektmappendatei des Projekts (`.sln`) öffnen.</span><span class="sxs-lookup"><span data-stu-id="ff268-300">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff268-301">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff268-301">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff268-302">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-302">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

<span data-ttu-id="ff268-303">Mit der Option `-o|--output` wird ein Ordner für das Projekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="ff268-303">The `-o|--output` option creates a folder for the project.</span></span> <span data-ttu-id="ff268-304">Wenn Sie einen Ordner für das Projekt erstellt haben und dieser Ordner in der Befehlsshell geöffnet ist, lassen Sie zum Erstellen des Projekts die Option `-o|--output` und den entsprechenden Wert aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-304">If you've created a folder for the project and the command shell is open in that folder, omit the `-o|--output` option and value to create the project.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="ff268-305">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="ff268-305">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff268-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff268-306">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="ff268-307">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorServerSignalRApp`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-307">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff268-308">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf `nuget.org` eingestellt ist.</span><span class="sxs-lookup"><span data-stu-id="ff268-308">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff268-309">Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-309">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="ff268-310">Wählen Sie in den Suchergebnissen das Paket [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-310">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="ff268-311">Legen Sie die Version so fest, dass sie zum freigegebenen Framework der App passt.</span><span class="sxs-lookup"><span data-stu-id="ff268-311">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="ff268-312">Wählen Sie **Installieren** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-312">Select **Install**.</span></span>

1. <span data-ttu-id="ff268-313">Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-313">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="ff268-314">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="ff268-314">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff268-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff268-315">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="ff268-316">Führen Sie im **integrierten Terminal** (**Ansicht** > **Terminal** auf der Symbolleiste) den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-316">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="ff268-317">Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ff268-317">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff268-318">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff268-318">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff268-319">Klicken Sie in der **Projektmappen**-Randleiste mit der rechten Maustaste auf das `BlazorServerSignalRApp`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-319">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff268-320">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="ff268-320">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff268-321">Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-321">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="ff268-322">Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem Paket [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).</span><span class="sxs-lookup"><span data-stu-id="ff268-322">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package.</span></span> <span data-ttu-id="ff268-323">Legen Sie die Version so fest, dass sie zum freigegebenen Framework der App passt.</span><span class="sxs-lookup"><span data-stu-id="ff268-323">Set the version to match the shared framework of the app.</span></span> <span data-ttu-id="ff268-324">Klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="ff268-324">Select **Add Package**.</span></span>

1. <span data-ttu-id="ff268-325">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="ff268-325">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff268-326">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff268-326">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff268-327">Führen Sie in einer Befehlsshell im Ordner für das Projekt den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-327">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

<span data-ttu-id="ff268-328">Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ff268-328">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="ff268-329">Hinzufügen des System.Text.Encodings.Web-Pakets</span><span class="sxs-lookup"><span data-stu-id="ff268-329">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="ff268-330">*Dieser Abschnitt gilt nur für Apps für ASP.NET Core Version 3.x.*</span><span class="sxs-lookup"><span data-stu-id="ff268-330">*This section only applies to apps for ASP.NET Core version 3.x.*</span></span>

<span data-ttu-id="ff268-331">Aufgrund eines Problems bei der Paketauflösung bei der Verwendung von [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in einer ASP.NET Core 3.x-App erfordert das Projekt einen Paketverweis für [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="ff268-331">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in an ASP.NET Core 3.x app, the project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="ff268-332">Das zugrunde liegende Problem wird in einem zukünftigen Patchrelease von .NET 5 gelöst.</span><span class="sxs-lookup"><span data-stu-id="ff268-332">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="ff268-333">Weitere Informationen finden Sie unter [System.Text.Json defines netcoreapp3.0 with no dependencies #45560](https://github.com/dotnet/runtime/issues/45560) (System.Text.Json definiert netcoreapp3.0 ohne Abhängigkeiten – Nr.45560).</span><span class="sxs-lookup"><span data-stu-id="ff268-333">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="ff268-334">Befolgen Sie den Leitfaden für Ihr bevorzugtes Tool, um [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) zum Projekt hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="ff268-334">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the project, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff268-335">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff268-335">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="ff268-336">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorServerSignalRApp`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-336">In **Solution Explorer**, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff268-337">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf `nuget.org` eingestellt ist.</span><span class="sxs-lookup"><span data-stu-id="ff268-337">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff268-338">Geben Sie bei Auswahl von **Durchsuchen** `System.Text.Encodings.Web` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-338">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="ff268-339">Wählen Sie in den Suchergebnissen das Paket [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-339">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package.</span></span> <span data-ttu-id="ff268-340">Wählen Sie die Version des Pakets aus, die zum verwendeten freigegebenen Framework passt.</span><span class="sxs-lookup"><span data-stu-id="ff268-340">Select the version of the package that matches the shared framework in use.</span></span> <span data-ttu-id="ff268-341">Wählen Sie **Installieren** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-341">Select **Install**.</span></span>

1. <span data-ttu-id="ff268-342">Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-342">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="ff268-343">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="ff268-343">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff268-344">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff268-344">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="ff268-345">Führen Sie im **integrierten Terminal** (**Ansicht**>**Terminal** in der Symbolleiste) die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-345">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="ff268-346">Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ff268-346">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff268-347">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff268-347">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff268-348">Klicken Sie in der **Projektmappen**-Randleiste mit der rechten Maustaste auf das `BlazorServerSignalRApp`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="ff268-348">In the **Solution** sidebar, right-click the `BlazorServerSignalRApp` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="ff268-349">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="ff268-349">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="ff268-350">Geben Sie bei Auswahl von **Durchsuchen** `System.Text.Encodings.Web` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-350">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="ff268-351">Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem Paket [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web), wählen Sie die richtige Version des Pakets für das verwendete freigegebene Framework aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="ff268-351">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package, select the correct version of the package that matches the shared framework in use, and select **Add Package**.</span></span>

1. <span data-ttu-id="ff268-352">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="ff268-352">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff268-353">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff268-353">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="ff268-354">Führen Sie in einer Befehlsshell im Ordner für das Projekt den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-354">In a command shell from the project's folder, execute the following command:</span></span>

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

<span data-ttu-id="ff268-355">Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="ff268-355">To add an earlier version of the package, supply the `--version {VERSION}` option, where the `{VERSION}` placeholder is the version of the package to add.</span></span>

---

::: moniker-end

## <a name="add-a-signalr-hub"></a><span data-ttu-id="ff268-356">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="ff268-356">Add a SignalR hub</span></span>

<span data-ttu-id="ff268-357">Erstellen Sie einen `Hubs`-Ordner (Plural), und fügen Sie die folgende `ChatHub`-Klasse (`Hubs/ChatHub.cs`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="ff268-357">Create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="ff268-358">Hinzufügen von Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="ff268-358">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="ff268-359">Öffnen Sie die Datei `Startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="ff268-359">Open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="ff268-360">Fügen Sie am Anfang der Datei die Namespaces für <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> und die `ChatHub`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="ff268-360">Add the namespaces for <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> and the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ff268-361">Fügen Sie Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ff268-361">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="ff268-362">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff268-362">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="ff268-363">Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.</span><span class="sxs-lookup"><span data-stu-id="ff268-363">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="ff268-364">Fügen Sie zwischen den Endpunkten für die Zuordnung des Blazor-Hubs und den clientseitigen Fallback einen Endpunkt für den Hub hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff268-364">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ff268-365">Fügen Sie Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="ff268-365">Add Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. <span data-ttu-id="ff268-366">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="ff268-366">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="ff268-367">Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.</span><span class="sxs-lookup"><span data-stu-id="ff268-367">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="ff268-368">Fügen Sie zwischen den Endpunkten für die Zuordnung des Blazor-Hubs und den clientseitigen Fallback einen Endpunkt für den Hub hinzu.</span><span class="sxs-lookup"><span data-stu-id="ff268-368">Between the endpoints for mapping the Blazor hub and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="ff268-369">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="ff268-369">Add Razor component code for chat</span></span>

1. <span data-ttu-id="ff268-370">Öffnen Sie die Datei `Pages/Index.razor` .</span><span class="sxs-lookup"><span data-stu-id="ff268-370">Open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="ff268-371">Ersetzen Sie das Markup durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ff268-371">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="ff268-372">Ersetzen Sie das Markup durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ff268-372">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="ff268-373">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="ff268-373">Run the app</span></span>

<span data-ttu-id="ff268-374">Befolgen Sie die Anleitungen für die Auswahl Ihrer Tools:</span><span class="sxs-lookup"><span data-stu-id="ff268-374">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff268-375">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff268-375">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ff268-376">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff268-376">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff268-377">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-377">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff268-378">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="ff268-378">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff268-379">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="ff268-379">The name and message are displayed on both pages instantly:</span></span>

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff268-381">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff268-381">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff268-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff268-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="ff268-383">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff268-383">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff268-384">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-384">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff268-385">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="ff268-385">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff268-386">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="ff268-386">The name and message are displayed on both pages instantly:</span></span>

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff268-388">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff268-388">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff268-389">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff268-389">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ff268-390">Drücken Sie <kbd>⌘</kbd>+<kbd>↩</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff268-390">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="ff268-391">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-391">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff268-392">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="ff268-392">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff268-393">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="ff268-393">The name and message are displayed on both pages instantly:</span></span>

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff268-395">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff268-395">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ff268-396">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ff268-396">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="ff268-397">Führen Sie in einer Befehlsshell im Ordner für das Projekt die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="ff268-397">In a command shell from the project's folder, execute the following commands:</span></span>

   ```dotnetcli
   dotnet run
   ```

1. <span data-ttu-id="ff268-398">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="ff268-398">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="ff268-399">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="ff268-399">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="ff268-400">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="ff268-400">The name and message are displayed on both pages instantly:</span></span>

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   <span data-ttu-id="ff268-402">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="ff268-402">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

::: zone-end

## <a name="next-steps"></a><span data-ttu-id="ff268-403">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="ff268-403">Next steps</span></span>

<span data-ttu-id="ff268-404">In diesem Tutorial haben Sie Folgendes gelernt:</span><span class="sxs-lookup"><span data-stu-id="ff268-404">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="ff268-405">Erstellen eines Blazor-Projekts</span><span class="sxs-lookup"><span data-stu-id="ff268-405">Create a Blazor project</span></span>
> * <span data-ttu-id="ff268-406">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="ff268-406">Add the SignalR client library</span></span>
> * <span data-ttu-id="ff268-407">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="ff268-407">Add a SignalR hub</span></span>
> * <span data-ttu-id="ff268-408">Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="ff268-408">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="ff268-409">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="ff268-409">Add Razor component code for chat</span></span>

<span data-ttu-id="ff268-410">Weitere Informationen zum Erstellen von Blazor-Apps finden Sie in der Blazor-Dokumentation:</span><span class="sxs-lookup"><span data-stu-id="ff268-410">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="ff268-411"><xref:blazor/index>
> [Bearertokenauthentifizierung mit Identity-Server, WebSockets und vom Server gesendeten Ereignissen](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="ff268-411"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff268-412">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ff268-412">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="ff268-413">Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="ff268-413">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
