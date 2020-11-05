---
title: 'Verwenden von ASP.NET Core :::no-loc(SignalR)::: mit :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: 'Erstellen Sie eine Chat-App, die ASP.NET Core :::no-loc(SignalR)::: mit :::no-loc(Blazor WebAssembly)::: verwendet.'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 81cbfb692ffbd0bb6335ccef6dd10ad6c20fb334
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052706"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="90ae5-103">Verwenden von ASP.NET Core :::no-loc(SignalR)::: mit :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="90ae5-103">Use ASP.NET Core :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="90ae5-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="90ae5-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="90ae5-105">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit :::no-loc(SignalR)::: mit :::no-loc(Blazor WebAssembly)::: beschrieben.</span><span class="sxs-lookup"><span data-stu-id="90ae5-105">This tutorial teaches the basics of building a real-time app using :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="90ae5-106">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="90ae5-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="90ae5-107">Erstellen eines gehosteten :::no-loc(Blazor WebAssembly):::-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="90ae5-107">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="90ae5-108">Hinzufügen der :::no-loc(SignalR):::-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="90ae5-108">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="90ae5-109">Hinzufügen eines :::no-loc(SignalR):::-Hubs</span><span class="sxs-lookup"><span data-stu-id="90ae5-109">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="90ae5-110">Hinzufügen von :::no-loc(SignalR):::-Diensten und eines Endpunkts zum :::no-loc(SignalR):::-Hub</span><span class="sxs-lookup"><span data-stu-id="90ae5-110">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="90ae5-111">Hinzufügen von :::no-loc(Razor):::-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="90ae5-111">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="90ae5-112">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Chat-App.</span><span class="sxs-lookup"><span data-stu-id="90ae5-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="90ae5-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="90ae5-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="90ae5-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="90ae5-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="90ae5-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90ae5-115">Visual Studio</span></span>](#tab/visual-studio)

<!-- * [Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload -->
* <span data-ttu-id="90ae5-116">[Visual Studio 2019 Version 16.8 oder höher (Vorschauversion)](https://visualstudio.microsoft.com/vs/preview/) mit der Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="90ae5-116">[Visual Studio 2019 16.8 or later (in preview)](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="90ae5-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90ae5-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90ae5-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="90ae5-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- * [Visual Studio for Mac version 8.8 or later (in preview)](https://visualstudio.microsoft.com/vs/mac/) -->
* [<span data-ttu-id="90ae5-119">Visual Studio für Mac, Version 8.8 oder höher (Vorschauversion)</span><span class="sxs-lookup"><span data-stu-id="90ae5-119">Visual Studio for Mac version 8.8 or later (in preview)</span></span>](/visualstudio/releasenotes/vs2019-mac-preview-relnotes)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="90ae5-120">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="90ae5-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="90ae5-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90ae5-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="90ae5-122">[Visual Studio 2019 Version 16.6 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="90ae5-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="90ae5-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90ae5-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90ae5-124">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="90ae5-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="90ae5-125">Visual Studio für Mac Version 8.6 oder höher</span><span class="sxs-lookup"><span data-stu-id="90ae5-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="90ae5-126">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="90ae5-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="90ae5-127">Erstellen eines gehosteten :::no-loc(Blazor WebAssembly):::-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="90ae5-127">Create a hosted :::no-loc(Blazor WebAssembly)::: app project</span></span>

<span data-ttu-id="90ae5-128">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="90ae5-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90ae5-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90ae5-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="90ae5-130">Visual Studio 16.8 oder höher und das .NET Core SDK 5.0.0 oder höher sind erforderlich.</span><span class="sxs-lookup"><span data-stu-id="90ae5-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="90ae5-131">Visual Studio 16.6 oder höher und das .NET Core SDK 3.1.300 oder höher sind erforderlich.</span><span class="sxs-lookup"><span data-stu-id="90ae5-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="90ae5-132">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="90ae5-132">Create a new project.</span></span>

1. <span data-ttu-id="90ae5-133">Klicken Sie auf **:::no-loc(Blazor):::-App** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="90ae5-133">Select **:::no-loc(Blazor)::: App** and select **Next**.</span></span>

1. <span data-ttu-id="90ae5-134">Geben Sie im Feld **Projektname** `:::no-loc(Blazor)::::::no-loc(SignalR):::App` ein.</span><span class="sxs-lookup"><span data-stu-id="90ae5-134">Type `:::no-loc(Blazor)::::::no-loc(SignalR):::App` in the **Project name** field.</span></span> <span data-ttu-id="90ae5-135">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="90ae5-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="90ae5-136">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-136">Select **Create**.</span></span>

1. <span data-ttu-id="90ae5-137">Wählen Sie die **:::no-loc(Blazor WebAssembly)::: App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-137">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span>

1. <span data-ttu-id="90ae5-138">Aktivieren Sie unter **Erweitert** das Kontrollkästchen **Von ASP.net Core gehostet**.</span><span class="sxs-lookup"><span data-stu-id="90ae5-138">Under **Advanced** , select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="90ae5-139">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="90ae5-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90ae5-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="90ae5-141">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="90ae5-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
   ```

1. <span data-ttu-id="90ae5-142">Öffnen Sie in Visual Studio Code den Projektordner der App.</span><span class="sxs-lookup"><span data-stu-id="90ae5-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="90ae5-143">Wenn das Dialogfeld angezeigt wird, um Assets zum Erstellen und Debuggen der App hinzuzufügen, wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="90ae5-144">Visual Studio Code fügt den `.vscode`-Ordner automatisch mit generierten `launch.json`- und `tasks.json`-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="90ae5-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90ae5-145">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="90ae5-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="90ae5-146">Installieren Sie die neueste Version von [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/), und führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="90ae5-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="90ae5-147">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="90ae5-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="90ae5-148">Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="90ae5-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="90ae5-149">Wählen Sie die **:::no-loc(Blazor WebAssembly)::: App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-149">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="90ae5-150">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="90ae5-150">Select **Next**.</span></span>

1. <span data-ttu-id="90ae5-151">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="90ae5-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="90ae5-152">Aktivieren Sie das Kontrollkästchen **ASP.NET Core Hosted** (Auf ASP.NET Core gehostet).</span><span class="sxs-lookup"><span data-stu-id="90ae5-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="90ae5-153">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="90ae5-153">Select **Next**.</span></span>

1. <span data-ttu-id="90ae5-154">Benennen Sie im Feld **Projektname** die App `:::no-loc(Blazor)::::::no-loc(SignalR):::App`.</span><span class="sxs-lookup"><span data-stu-id="90ae5-154">In the **Project Name** field, name the app `:::no-loc(Blazor)::::::no-loc(SignalR):::App`.</span></span> <span data-ttu-id="90ae5-155">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-155">Select **Create**.</span></span>

   <span data-ttu-id="90ae5-156">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="90ae5-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="90ae5-157">Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="90ae5-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="90ae5-158">Öffnen Sie das Projekt, indem Sie zum Projektordner navigieren und die Projektmappendatei des Projekts (`.sln`) öffnen.</span><span class="sxs-lookup"><span data-stu-id="90ae5-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="90ae5-159">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="90ae5-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="90ae5-160">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="90ae5-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="90ae5-161">Hinzufügen der :::no-loc(SignalR):::-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="90ae5-161">Add the :::no-loc(SignalR)::: client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90ae5-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90ae5-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="90ae5-163">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-163">In **Solution Explorer** , right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="90ae5-164">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten** , ob die **Paketquelle** auf `nuget.org` eingestellt ist.</span><span class="sxs-lookup"><span data-stu-id="90ae5-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="90ae5-165">Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="90ae5-165">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="90ae5-166">Wählen Sie in den Suchergebnissen das [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client)-Paket aus, und klicken Sie dann auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="90ae5-166">In the search results, select the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="90ae5-167">Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="90ae5-168">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="90ae5-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="90ae5-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90ae5-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="90ae5-170">Führen Sie im **integrierten Terminal** ( **Ansicht** > **Terminal** in der Symbolleiste) die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="90ae5-170">In the **Integrated Terminal** ( **View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90ae5-171">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="90ae5-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="90ae5-172">Klicken Sie in der **Projektmappen** -Randleiste mit der rechten Maustaste auf das `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-172">In the **Solution** sidebar, right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="90ae5-173">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten** , ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="90ae5-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="90ae5-174">Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="90ae5-174">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="90ae5-175">Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client)-Paket, und wählen Sie **Paket hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="90ae5-176">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="90ae5-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="90ae5-177">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="90ae5-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="90ae5-178">Führen Sie in einer Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="90ae5-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd :::no-loc(Blazor)::::::no-loc(SignalR):::App
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="90ae5-179">Hinzufügen eines :::no-loc(SignalR):::-Hubs</span><span class="sxs-lookup"><span data-stu-id="90ae5-179">Add a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="90ae5-180">Erstellen Sie im `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server`-Projekt einen `Hubs`-Ordner (Plural), und fügen Sie die folgende `ChatHub`-Klasse (`Hubs/ChatHub.cs`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="90ae5-180">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="90ae5-181">Hinzufügen von Diensten und eines Endpunkts zum :::no-loc(SignalR):::-Hub</span><span class="sxs-lookup"><span data-stu-id="90ae5-181">Add services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>

1. <span data-ttu-id="90ae5-182">Öffnen Sie im Projekt `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` die Datei `Startup.cs`.</span><span class="sxs-lookup"><span data-stu-id="90ae5-182">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="90ae5-183">Fügen Sie am Anfang der Datei den Namespace für die `ChatHub`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="90ae5-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using :::no-loc(Blazor)::::::no-loc(SignalR):::App.Server.Hubs;
   ```

1. <span data-ttu-id="90ae5-184">Fügen Sie :::no-loc(SignalR)::: und Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="90ae5-184">Add :::no-loc(SignalR)::: and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="90ae5-185">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="90ae5-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="90ae5-186">Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.</span><span class="sxs-lookup"><span data-stu-id="90ae5-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="90ae5-187">Fügen Sie zwischen den Endpunkten für Controller und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu.</span><span class="sxs-lookup"><span data-stu-id="90ae5-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="90ae5-188">Hinzufügen von :::no-loc(Razor):::-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="90ae5-188">Add :::no-loc(Razor)::: component code for chat</span></span>

1. <span data-ttu-id="90ae5-189">Öffnen Sie im Projekt `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` die Datei `Pages/Index.razor`.</span><span class="sxs-lookup"><span data-stu-id="90ae5-189">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="90ae5-190">Ersetzen Sie das Markup durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="90ae5-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="90ae5-191">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="90ae5-191">Run the app</span></span>

1. <span data-ttu-id="90ae5-192">Befolgen Sie die Anleitungen für die Auswahl Ihrer Tools:</span><span class="sxs-lookup"><span data-stu-id="90ae5-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="90ae5-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="90ae5-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="90ae5-194">Wählen Sie im **Projektmappen-Explorer** das `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server`-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-194">In **Solution Explorer** , select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="90ae5-195">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="90ae5-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="90ae5-196">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="90ae5-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="90ae5-197">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="90ae5-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="90ae5-198">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="90ae5-198">The name and message are displayed on both pages instantly:</span></span>

   ![:::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly):::Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="90ae5-200">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="90ae5-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="90ae5-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="90ae5-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="90ae5-202">Wenn VS Code anbietet, ein Startprofil für die Server-App zu erstellen (`.vscode/launch.json`), scheint der `program`-Eintrag, ähnlich wie der folgende, auf die Assembly der App (`{APPLICATION NAME}.Server.dll`) zu zeigen:</span><span class="sxs-lookup"><span data-stu-id="90ae5-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="90ae5-203">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="90ae5-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="90ae5-204">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="90ae5-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="90ae5-205">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="90ae5-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="90ae5-206">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="90ae5-206">The name and message are displayed on both pages instantly:</span></span>

   ![:::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly):::Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="90ae5-208">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="90ae5-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="90ae5-209">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="90ae5-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="90ae5-210">Wählen Sie in der **Projektmappen** -Randleiste das `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server`-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="90ae5-210">In the **Solution** sidebar, select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="90ae5-211">Drücken Sie <kbd>⌘</kbd>+<kbd>↩</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="90ae5-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="90ae5-212">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="90ae5-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="90ae5-213">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="90ae5-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="90ae5-214">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="90ae5-214">The name and message are displayed on both pages instantly:</span></span>

   ![:::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly):::Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="90ae5-216">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="90ae5-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="90ae5-217">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="90ae5-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="90ae5-218">Führen Sie in einer Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="90ae5-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="90ae5-219">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="90ae5-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="90ae5-220">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="90ae5-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="90ae5-221">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="90ae5-221">The name and message are displayed on both pages instantly:</span></span>

   ![:::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly):::Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="90ae5-223">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="90ae5-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="90ae5-224">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="90ae5-224">Next steps</span></span>

<span data-ttu-id="90ae5-225">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="90ae5-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="90ae5-226">Erstellen eines gehosteten :::no-loc(Blazor WebAssembly):::-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="90ae5-226">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="90ae5-227">Hinzufügen der :::no-loc(SignalR):::-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="90ae5-227">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="90ae5-228">Hinzufügen eines :::no-loc(SignalR):::-Hubs</span><span class="sxs-lookup"><span data-stu-id="90ae5-228">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="90ae5-229">Hinzufügen von :::no-loc(SignalR):::-Diensten und eines Endpunkts zum :::no-loc(SignalR):::-Hub</span><span class="sxs-lookup"><span data-stu-id="90ae5-229">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="90ae5-230">Hinzufügen von :::no-loc(Razor):::-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="90ae5-230">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="90ae5-231">Weitere Informationen zum Erstellen von :::no-loc(Blazor):::-Apps finden Sie in der :::no-loc(Blazor):::-Dokumentation:</span><span class="sxs-lookup"><span data-stu-id="90ae5-231">To learn more about building :::no-loc(Blazor)::: apps, see the :::no-loc(Blazor)::: documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="90ae5-232"><xref:blazor/index>
> [Bearertokenauthentifizierung mit :::no-loc(Identity):::-Server, WebSockets und vom Server gesendeten Ereignissen](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="90ae5-232"><xref:blazor/index>
[Bearer token authentication with :::no-loc(Identity)::: Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90ae5-233">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="90ae5-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="90ae5-234">Ursprungsübergreifende :::no-loc(SignalR):::-Aushandlung für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="90ae5-234">:::no-loc(SignalR)::: cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
