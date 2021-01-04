---
title: Tools für ASP.NET-Core Blazor
author: guardrex
description: Informieren Sie sich über die Tools, die zum Erstellen von Blazor-Apps verfügbar sind.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2020
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 29f1a1f211688a1edcd31c7230e7216df7c89eef
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506811"
---
# <a name="tooling-for-aspnet-core-no-locblazor"></a><span data-ttu-id="f9f46-103">Tools für ASP.NET-Core Blazor</span><span class="sxs-lookup"><span data-stu-id="f9f46-103">Tooling for ASP.NET Core Blazor</span></span>

<span data-ttu-id="f9f46-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f9f46-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="f9f46-105">Installieren Sie die neueste Version von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**.</span><span class="sxs-lookup"><span data-stu-id="f9f46-105">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="f9f46-106">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="f9f46-106">Create a new project.</span></span>

1. <span data-ttu-id="f9f46-107">Klicken Sie auf **Blazor-App**.</span><span class="sxs-lookup"><span data-stu-id="f9f46-107">Select **Blazor App**.</span></span> <span data-ttu-id="f9f46-108">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f9f46-108">Select **Next**.</span></span>

1. <span data-ttu-id="f9f46-109">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="f9f46-109">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="f9f46-110">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="f9f46-110">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f9f46-111">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f9f46-111">Select **Create**.</span></span>

1. <span data-ttu-id="f9f46-112">Wählen Sie für eine Blazor WebAssembly-Benutzeroberfläche die **Blazor WebAssembly-App-** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="f9f46-112">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f9f46-113">Wählen Sie für eine Blazor Server-Benutzeroberfläche die **Blazor Server-App-** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="f9f46-113">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f9f46-114">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f9f46-114">Select **Create**.</span></span>

   <span data-ttu-id="f9f46-115">Aktivieren Sie für das gehostete Modell von Blazor WebAssembly das Kontrollkästchen **ASP.NET Core, gehostet**.</span><span class="sxs-lookup"><span data-stu-id="f9f46-115">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="f9f46-116">Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="f9f46-116">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f9f46-117">Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f9f46-117">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="f9f46-118">Weitere Informationen zum Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig finden Sie hier: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="f9f46-118">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="f9f46-119">Installieren Sie die neueste Version des [.NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="f9f46-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="f9f46-120">Wenn Sie das SDK zuvor installiert haben, können Sie die installierte Version ermitteln, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="f9f46-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="f9f46-121">Installieren Sie die neueste Version von [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="f9f46-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="f9f46-122">Installieren Sie die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="f9f46-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="f9f46-123">Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="f9f46-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="f9f46-124">Fügen Sie dem Befehl für ein gehostetes Blazor WebAssembly-Modell die Hostingoption (`-ho` oder `--hosted`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="f9f46-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>
   
   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```
   
   <span data-ttu-id="f9f46-125">Für eine Blazor Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="f9f46-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="f9f46-126">Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="f9f46-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f9f46-127">Öffnen Sie in Visual Studio Code den Ordner `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="f9f46-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="f9f46-128">Die IDE fordert an, dass Sie Ressourcen zum Erstellen und Debuggen des Projekts hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="f9f46-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="f9f46-129">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="f9f46-129">Select **Yes**.</span></span>

1. <span data-ttu-id="f9f46-130">Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f9f46-130">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="f9f46-131">Festlegen eines Entwicklungszertifikats als vertrauenswürdig</span><span class="sxs-lookup"><span data-stu-id="f9f46-131">Trust a development certificate</span></span>

<span data-ttu-id="f9f46-132">Es gibt keine zentrale Möglichkeit, ein Zertifikat in Linux als vertrauenswürdig festzulegen.</span><span class="sxs-lookup"><span data-stu-id="f9f46-132">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="f9f46-133">In der Regel wird einer der folgenden Ansätze verwendet:</span><span class="sxs-lookup"><span data-stu-id="f9f46-133">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="f9f46-134">Ausschließen der App-URL über die Ausschlussliste des Browsers.</span><span class="sxs-lookup"><span data-stu-id="f9f46-134">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="f9f46-135">Festlegen aller selbstsignierten Zertifikate für `localhost` als vertrauenswürdig.</span><span class="sxs-lookup"><span data-stu-id="f9f46-135">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="f9f46-136">Hinzufügen des Zertifikats zur Liste der vertrauenswürdigen Zertifikate im Browser.</span><span class="sxs-lookup"><span data-stu-id="f9f46-136">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="f9f46-137">Weitere Informationen finden Sie in der Dokumentation zu Ihrem Browseranbieter und Ihrer Linux-Distribution.</span><span class="sxs-lookup"><span data-stu-id="f9f46-137">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="f9f46-138">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="f9f46-138">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="f9f46-139">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="f9f46-139">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="f9f46-140">Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="f9f46-140">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="f9f46-141">Wählen Sie für eine Blazor WebAssembly-Benutzeroberfläche die **Blazor WebAssembly-App-** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="f9f46-141">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="f9f46-142">Wählen Sie für eine Blazor Server-Benutzeroberfläche die **Blazor Server-App-** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="f9f46-142">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="f9f46-143">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f9f46-143">Select **Next**.</span></span>

   <span data-ttu-id="f9f46-144">Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="f9f46-144">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="f9f46-145">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="f9f46-145">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="f9f46-146">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="f9f46-146">Select **Next**.</span></span>

1. <span data-ttu-id="f9f46-147">Aktivieren Sie für das gehostete Modell von Blazor WebAssembly das Kontrollkästchen **ASP.NET Core, gehostet**.</span><span class="sxs-lookup"><span data-stu-id="f9f46-147">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="f9f46-148">Benennen Sie im Feld **Projektname** die App `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="f9f46-148">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="f9f46-149">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f9f46-149">Select **Create**.</span></span>

1. <span data-ttu-id="f9f46-150">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App *ohne Debugger* auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f9f46-150">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="f9f46-151">Führen Sie die App entweder über **Ausführen** > **Debuggen starten** oder über die Schaltfläche „Ausführen“ (&#9654;) *mit dem Debugger aus*.</span><span class="sxs-lookup"><span data-stu-id="f9f46-151">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="f9f46-152">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="f9f46-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="f9f46-153">Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="f9f46-153">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="f9f46-154">Weitere Informationen zum Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig finden Sie hier: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="f9f46-154">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-no-locblazor-development"></a><span data-ttu-id="f9f46-155">Verwenden von Visual Studio Code für die plattformübergreifende Blazor-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="f9f46-155">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="f9f46-156">[Visual Studio Code](https://code.visualstudio.com/) ist eine plattformübergreifende Open-Source-Entwicklungsumgebung (Integrated Development Environment, IDE), mit der Blazor-Apps entwickelt werden können.</span><span class="sxs-lookup"><span data-stu-id="f9f46-156">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="f9f46-157">Verwenden Sie die .NET-CLI zum Erstellen einer neuen Blazor-App für die Entwicklung mit Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="f9f46-157">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="f9f46-158">Weitere Informationen finden Sie in der [Linux-Version dieses Artikels](/aspnet/core/blazor/tooling?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="f9f46-158">For more information, see the [Linux version of this article](/aspnet/core/blazor/tooling?pivots=linux).</span></span>

## <a name="no-locblazor-template-options"></a><span data-ttu-id="f9f46-159">Blazor-Vorlagenoptionen</span><span class="sxs-lookup"><span data-stu-id="f9f46-159">Blazor template options</span></span>

<span data-ttu-id="f9f46-160">Das Blazor-Framework bietet für beide Blazor-Hostingmodelle Vorlagen zum Erstellen neuer Apps.</span><span class="sxs-lookup"><span data-stu-id="f9f46-160">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="f9f46-161">Mithilfe der Vorlagen können neue Blazor-Projekte und -Projektmappen erstellt werden, unabhängig von den Tools, die Sie für die Blazor-Entwicklung auswählen (Visual Studio, Visual Studio für Mac, Visual Studio Code oder die .NET-CLI):</span><span class="sxs-lookup"><span data-stu-id="f9f46-161">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="f9f46-162">Blazor WebAssembly-Projektvorlage: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="f9f46-162">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="f9f46-163">Blazor Server-Projektvorlage: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="f9f46-163">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="f9f46-164">Weitere Informationen zur Blazor-Hostingmodellen finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="f9f46-164">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="f9f46-165">Vorlagenoptionen sind verfügbar, indem Sie die Hilfeoption (`-h` oder `--help`) in einer Befehlsshell an den CLI-Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) übergeben:</span><span class="sxs-lookup"><span data-stu-id="f9f46-165">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm --h
dotnet new blazorserver --h
```
