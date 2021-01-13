---
title: Verwenden von ASP.NET Core-SignalR mit gehosteter Blazor WebAssembly-App
author: guardrex
description: Erstellen Sie eine Chat-App, die ASP.NET Core SignalR mit Blazor WebAssembly verwendet.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797352"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="aaefa-103">Verwenden von ASP.NET Core-SignalR mit gehosteter Blazor WebAssembly-App</span><span class="sxs-lookup"><span data-stu-id="aaefa-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="aaefa-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="aaefa-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="aaefa-105">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR mit Blazor WebAssembly beschrieben.</span><span class="sxs-lookup"><span data-stu-id="aaefa-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="aaefa-106">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="aaefa-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aaefa-107">Erstellen eines gehosteten Blazor WebAssembly-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="aaefa-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="aaefa-108">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="aaefa-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="aaefa-109">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="aaefa-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="aaefa-110">Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="aaefa-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="aaefa-111">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="aaefa-111">Add Razor component code for chat</span></span>

<span data-ttu-id="aaefa-112">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Chat-App.</span><span class="sxs-lookup"><span data-stu-id="aaefa-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="aaefa-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aaefa-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="aaefa-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="aaefa-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="aaefa-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaefa-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aaefa-116">[Visual Studio 2019 Version 16.8 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="aaefa-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaefa-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaefa-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaefa-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aaefa-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="aaefa-119">Visual Studio für Mac ab Version 8.8</span><span class="sxs-lookup"><span data-stu-id="aaefa-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="aaefa-120">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aaefa-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="aaefa-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaefa-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aaefa-122">[Visual Studio 2019 Version 16.6 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="aaefa-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaefa-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaefa-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaefa-124">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aaefa-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="aaefa-125">Visual Studio für Mac Version 8.6 oder höher</span><span class="sxs-lookup"><span data-stu-id="aaefa-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="aaefa-126">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aaefa-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="aaefa-127">Erstellen eines gehosteten Blazor WebAssembly-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="aaefa-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="aaefa-128">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="aaefa-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaefa-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaefa-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="aaefa-130">Visual Studio 16.8 oder höher und das .NET Core SDK 5.0.0 oder höher sind erforderlich.</span><span class="sxs-lookup"><span data-stu-id="aaefa-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="aaefa-131">Visual Studio 16.6 oder höher und das .NET Core SDK 3.1.300 oder höher sind erforderlich.</span><span class="sxs-lookup"><span data-stu-id="aaefa-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="aaefa-132">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="aaefa-132">Create a new project.</span></span>

1. <span data-ttu-id="aaefa-133">Klicken Sie auf **Blazor-App** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="aaefa-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="aaefa-134">Geben Sie im Feld **Projektname** `BlazorSignalRApp` ein.</span><span class="sxs-lookup"><span data-stu-id="aaefa-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="aaefa-135">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="aaefa-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="aaefa-136">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-136">Select **Create**.</span></span>

1. <span data-ttu-id="aaefa-137">Wählen Sie die **Blazor WebAssembly App**-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="aaefa-138">Aktivieren Sie unter **Erweitert** das Kontrollkästchen **Von ASP.net Core gehostet**.</span><span class="sxs-lookup"><span data-stu-id="aaefa-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="aaefa-139">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaefa-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaefa-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="aaefa-141">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="aaefa-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="aaefa-142">Öffnen Sie in Visual Studio Code den Projektordner der App.</span><span class="sxs-lookup"><span data-stu-id="aaefa-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="aaefa-143">Wenn das Dialogfeld angezeigt wird, um Assets zum Erstellen und Debuggen der App hinzuzufügen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="aaefa-144">Visual Studio Code fügt den `.vscode`-Ordner automatisch mit generierten `launch.json`- und `tasks.json`-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="aaefa-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaefa-145">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aaefa-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="aaefa-146">Installieren Sie die neueste Version von [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/), und führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="aaefa-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="aaefa-147">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="aaefa-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="aaefa-148">Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="aaefa-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="aaefa-149">Wählen Sie die **Blazor WebAssembly App**-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="aaefa-150">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="aaefa-150">Select **Next**.</span></span>

1. <span data-ttu-id="aaefa-151">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="aaefa-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="aaefa-152">Aktivieren Sie das Kontrollkästchen **ASP.NET Core Hosted** (Auf ASP.NET Core gehostet).</span><span class="sxs-lookup"><span data-stu-id="aaefa-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="aaefa-153">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="aaefa-153">Select **Next**.</span></span>

1. <span data-ttu-id="aaefa-154">Benennen Sie im Feld **Projektname** die App `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="aaefa-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="aaefa-155">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-155">Select **Create**.</span></span>

   <span data-ttu-id="aaefa-156">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="aaefa-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="aaefa-157">Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="aaefa-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="aaefa-158">Öffnen Sie das Projekt, indem Sie zum Projektordner navigieren und die Projektmappendatei des Projekts (`.sln`) öffnen.</span><span class="sxs-lookup"><span data-stu-id="aaefa-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="aaefa-159">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aaefa-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="aaefa-160">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="aaefa-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="aaefa-161">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="aaefa-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaefa-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaefa-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="aaefa-163">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorSignalRApp.Client`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="aaefa-164">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf `nuget.org` eingestellt ist.</span><span class="sxs-lookup"><span data-stu-id="aaefa-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="aaefa-165">Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="aaefa-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="aaefa-166">Wählen Sie in den Suchergebnissen das [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)-Paket aus, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="aaefa-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="aaefa-167">Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="aaefa-168">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="aaefa-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaefa-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaefa-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="aaefa-170">Führen Sie im **integrierten Terminal** (**Ansicht** > **Terminal** in der Symbolleiste) die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="aaefa-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaefa-171">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aaefa-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="aaefa-172">Klicken Sie in der **Projektmappen**-Randleiste mit der rechten Maustaste auf das `BlazorSignalRApp.Client`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="aaefa-173">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="aaefa-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="aaefa-174">Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="aaefa-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="aaefa-175">Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)-Paket, und wählen Sie **Paket hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="aaefa-176">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="aaefa-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="aaefa-177">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aaefa-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="aaefa-178">Führen Sie in einer Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="aaefa-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="aaefa-179">Hinzufügen des System.Text.Encodings.Web-Pakets</span><span class="sxs-lookup"><span data-stu-id="aaefa-179">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="aaefa-180">Aufgrund eines Problems bei der Paketauflösung bei der Verwendung von [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in einer ASP.NET Core 3.1-App erfordert das `BlazorSignalRApp.Server`-Projekt einen Paketverweis für [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span><span class="sxs-lookup"><span data-stu-id="aaefa-180">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in an ASP.NET Core 3.1 app, the `BlazorSignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="aaefa-181">Das zugrunde liegende Problem wird in einem zukünftigen Patchrelease von .NET 5 gelöst.</span><span class="sxs-lookup"><span data-stu-id="aaefa-181">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="aaefa-182">Weitere Informationen finden Sie unter [System.Text.Json defines netcoreapp3.0 with no dependencies #45560](https://github.com/dotnet/runtime/issues/45560) (System.Text.Json definiert netcoreapp3.0 ohne Abhängigkeiten – Nr.45560).</span><span class="sxs-lookup"><span data-stu-id="aaefa-182">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="aaefa-183">Um [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) dem `BlazorSignalRApp.Server`-Projekt der unter ASP.NET Core 3.1 gehosteten Blazor-Lösung hinzuzufügen, befolgen Sie die Anleitungen für Ihre Toolsauswahl:</span><span class="sxs-lookup"><span data-stu-id="aaefa-183">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorSignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaefa-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaefa-184">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="aaefa-185">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorSignalRApp.Server`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-185">In **Solution Explorer**, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="aaefa-186">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf `nuget.org` eingestellt ist.</span><span class="sxs-lookup"><span data-stu-id="aaefa-186">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="aaefa-187">Geben Sie bei Auswahl von **Durchsuchen** `System.Text.Encodings.Web` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="aaefa-187">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="aaefa-188">Wählen Sie in den Suchergebnissen das [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web)-Paket aus, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="aaefa-188">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Install**.</span></span>

1. <span data-ttu-id="aaefa-189">Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-189">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="aaefa-190">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="aaefa-190">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaefa-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaefa-191">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="aaefa-192">Führen Sie im **integrierten Terminal** (**Ansicht**>**Terminal** in der Symbolleiste) die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="aaefa-192">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaefa-193">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aaefa-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="aaefa-194">Klicken Sie in der **Projektmappen**-Randleiste mit der rechten Maustaste auf das `BlazorSignalRApp.Server`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-194">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="aaefa-195">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="aaefa-195">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="aaefa-196">Geben Sie bei Auswahl von **Durchsuchen** `System.Text.Encodings.Web` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="aaefa-196">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="aaefa-197">Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web)-Paket, und wählen Sie **Paket hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-197">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Add Package**.</span></span>

1. <span data-ttu-id="aaefa-198">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="aaefa-198">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="aaefa-199">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aaefa-199">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="aaefa-200">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="aaefa-200">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="aaefa-201">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="aaefa-201">Add a SignalR hub</span></span>

<span data-ttu-id="aaefa-202">Erstellen Sie im `BlazorSignalRApp.Server`-Projekt einen `Hubs`-Ordner (Plural), und fügen Sie die folgende `ChatHub`-Klasse (`Hubs/ChatHub.cs`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="aaefa-202">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="aaefa-203">Hinzufügen von Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="aaefa-203">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="aaefa-204">Öffnen Sie im Projekt `BlazorSignalRApp.Server` die Datei `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="aaefa-204">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="aaefa-205">Fügen Sie am Anfang der Datei den Namespace für die `ChatHub`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="aaefa-205">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="aaefa-206">Fügen Sie SignalR und Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="aaefa-206">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. <span data-ttu-id="aaefa-207">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="aaefa-207">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="aaefa-208">Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.</span><span class="sxs-lookup"><span data-stu-id="aaefa-208">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="aaefa-209">Fügen Sie zwischen den Endpunkten für Controller und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu.</span><span class="sxs-lookup"><span data-stu-id="aaefa-209">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="aaefa-210">Fügen Sie SignalR und Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="aaefa-210">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. <span data-ttu-id="aaefa-211">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="aaefa-211">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="aaefa-212">Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.</span><span class="sxs-lookup"><span data-stu-id="aaefa-212">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="aaefa-213">Fügen Sie zwischen den Endpunkten für Controller und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu.</span><span class="sxs-lookup"><span data-stu-id="aaefa-213">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="aaefa-214">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="aaefa-214">Add Razor component code for chat</span></span>

1. <span data-ttu-id="aaefa-215">Öffnen Sie im Projekt `BlazorSignalRApp.Client` die Datei `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="aaefa-215">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="aaefa-216">Ersetzen Sie das Markup durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="aaefa-216">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="aaefa-217">Ersetzen Sie das Markup durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="aaefa-217">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="aaefa-218">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="aaefa-218">Run the app</span></span>

<span data-ttu-id="aaefa-219">Befolgen Sie die Anleitungen für die Auswahl Ihrer Tools:</span><span class="sxs-lookup"><span data-stu-id="aaefa-219">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aaefa-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aaefa-220">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="aaefa-221">Wählen Sie im **Projektmappen-Explorer** das `BlazorSignalRApp.Server`-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-221">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="aaefa-222">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="aaefa-222">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="aaefa-223">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="aaefa-223">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="aaefa-224">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="aaefa-224">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="aaefa-225">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="aaefa-225">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssemblyBeispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="aaefa-227">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="aaefa-227">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aaefa-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aaefa-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="aaefa-229">Wenn VS Code anbietet, ein Startprofil für die Server-App zu erstellen (`.vscode/launch.json`), scheint der `program`-Eintrag, ähnlich wie der folgende, auf die Assembly der App (`{APPLICATION NAME}.Server.dll`) zu zeigen:</span><span class="sxs-lookup"><span data-stu-id="aaefa-229">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="aaefa-230">Wenn VS Code anbietet, ein Startprofil für die Server-App zu erstellen (`.vscode/launch.json`), scheint der `program`-Eintrag, ähnlich wie der folgende, auf die Assembly der App (`{APPLICATION NAME}.Server.dll`) zu zeigen:</span><span class="sxs-lookup"><span data-stu-id="aaefa-230">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="aaefa-231">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="aaefa-231">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="aaefa-232">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="aaefa-232">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="aaefa-233">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="aaefa-233">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="aaefa-234">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="aaefa-234">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssemblyBeispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="aaefa-236">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="aaefa-236">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aaefa-237">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aaefa-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="aaefa-238">Wählen Sie in der **Projektmappen**-Randleiste das `BlazorSignalRApp.Server`-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="aaefa-238">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="aaefa-239">Drücken Sie <kbd>⌘</kbd>+<kbd>↩</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="aaefa-239">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="aaefa-240">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="aaefa-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="aaefa-241">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="aaefa-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="aaefa-242">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="aaefa-242">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssemblyBeispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="aaefa-244">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="aaefa-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="aaefa-245">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aaefa-245">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="aaefa-246">Führen Sie in einer Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="aaefa-246">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="aaefa-247">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="aaefa-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="aaefa-248">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="aaefa-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="aaefa-249">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="aaefa-249">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssemblyBeispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="aaefa-251">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="aaefa-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="aaefa-252">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="aaefa-252">Next steps</span></span>

<span data-ttu-id="aaefa-253">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="aaefa-253">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aaefa-254">Erstellen eines gehosteten Blazor WebAssembly-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="aaefa-254">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="aaefa-255">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="aaefa-255">Add the SignalR client library</span></span>
> * <span data-ttu-id="aaefa-256">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="aaefa-256">Add a SignalR hub</span></span>
> * <span data-ttu-id="aaefa-257">Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="aaefa-257">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="aaefa-258">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="aaefa-258">Add Razor component code for chat</span></span>

<span data-ttu-id="aaefa-259">Weitere Informationen zum Erstellen von Blazor-Apps finden Sie in der Blazor-Dokumentation:</span><span class="sxs-lookup"><span data-stu-id="aaefa-259">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="aaefa-260"><xref:blazor/index>
> [Bearertokenauthentifizierung mit Identity-Server, WebSockets und vom Server gesendeten Ereignissen](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="aaefa-260"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aaefa-261">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="aaefa-261">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="aaefa-262">Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="aaefa-262">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
