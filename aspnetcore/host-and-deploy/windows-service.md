---
title: Hosten von ASP.NET Core in einem Windows-Dienst
author: rick-anderson
description: Erfahren Sie, wie eine ASP.NET Core-App in einem Windows-Dienst gehostet wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
uid: host-and-deploy/windows-service
ms.openlocfilehash: d4df10f9450ca956d7b1a4297caa63cdd0caf23e
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113750"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="49eb6-103">Hosten von ASP.NET Core in einem Windows-Dienst</span><span class="sxs-lookup"><span data-stu-id="49eb6-103">Host ASP.NET Core in a Windows Service</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="49eb6-104">Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-104">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="49eb6-105">Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.</span><span class="sxs-lookup"><span data-stu-id="49eb6-105">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="49eb6-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49eb6-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49eb6-107">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="49eb6-107">Prerequisites</span></span>

* [<span data-ttu-id="49eb6-108">ASP.NET Core SDK 2.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="49eb6-108">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="49eb6-109">PowerShell 6.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="49eb6-109">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="49eb6-110">Workerdienstvorlage</span><span class="sxs-lookup"><span data-stu-id="49eb6-110">Worker Service template</span></span>

<span data-ttu-id="49eb6-111">Die ASP.NET Core-Workerdienstvorlage dient als Ausgangspunkt für das Programmieren von zeitintensiven Dienstanwendungen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-111">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="49eb6-112">So können Sie diese Vorlage als Grundlage für eine Windows-Dienstanwendung verwenden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-112">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="49eb6-113">Erstellen Sie eine Workerdienstanwendung über die .NET Core-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="49eb6-113">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="49eb6-114">Befolgen Sie die Anweisungen im Abschnitt [App-Konfiguration](#app-configuration), um die Workerdienstanwendung so zu aktualisieren, dass sie als Windows-Dienst ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="49eb6-114">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="49eb6-115">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="49eb6-115">App configuration</span></span>

<span data-ttu-id="49eb6-116">Die App erfordert einen Paketverweis auf [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="49eb6-116">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="49eb6-117">Beim Erstellen des Hosts wird `IHostBuilder.UseWindowsService` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-117">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="49eb6-118">Wenn die App als Windows-Dienst ausgeführt wird, sorgt die Methode für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="49eb6-118">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="49eb6-119">Sie legt die Lebensdauer des Hosts auf `WindowsServiceLifetime` fest.</span><span class="sxs-lookup"><span data-stu-id="49eb6-119">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="49eb6-120">[AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) wird als [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-120">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="49eb6-121">Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="49eb6-121">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="49eb6-122">Ermöglicht die Protokollierung zum Ereignisprotokoll:</span><span class="sxs-lookup"><span data-stu-id="49eb6-122">Enables logging to the event log:</span></span>
  * <span data-ttu-id="49eb6-123">Der Anwendungsname wird als Standardname für die Quelle verwendet.</span><span class="sxs-lookup"><span data-stu-id="49eb6-123">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="49eb6-124">Für eine App basierend auf einer ASP.NET Core-Vorlage, die `CreateDefaultBuilder` zur Erstellung des Hosts aufruft, lautet die Standardprotokollebene *Warnung* oder höher.</span><span class="sxs-lookup"><span data-stu-id="49eb6-124">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="49eb6-125">Setzen Sie die Standardprotokollebene mit dem `Logging:EventLog:LogLevel:Default`-Schlüssel in *appsettings.json*/*appsettings.{Environment}.json* oder einem anderen Konfigurationsanbieter außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="49eb6-125">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="49eb6-126">Nur Administratoren können neue Ereignisquellen erstellen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-126">Only administrators can create new event sources.</span></span> <span data-ttu-id="49eb6-127">Wenn keine Ereignisquellen mithilfe des Anwendungsnamens erstellt werden können, wird in der *Anwendungsquelle* eine Warnung protokolliert, und die Ereignisprotokolle werden deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="49eb6-127">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="49eb6-128">In `CreateHostBuilder` von *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="49eb6-128">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="49eb6-129">In diesem Thema werden die folgenden Beispiel-Apps behandelt:</span><span class="sxs-lookup"><span data-stu-id="49eb6-129">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="49eb6-130">Beispiel für einen Hintergrundworkerdienst: Dabei handelt es sich um ein Beispiel einer nicht webbasierten App, die auf der [Workerdienstvorlage](#worker-service-template) basiert und [gehostete Dienste](xref:fundamentals/host/hosted-services) für Hintergrundaufgaben verwendet.</span><span class="sxs-lookup"><span data-stu-id="49eb6-130">Background Worker Service Sample: A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="49eb6-131">Beispiel für einen Web-App-Dienst: Dabei handelt es sich um ein Razor Pages-Web-App-Beispiel, das als Windows-Dienst mit [gehosteten Diensten](xref:fundamentals/host/hosted-services) für Hintergrundaufgaben ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-131">Web App Service Sample: A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="49eb6-132">MVC-Anleitungen finden Sie in den Artikeln unter <xref:mvc/overview> und <xref:migration/22-to-30>.</span><span class="sxs-lookup"><span data-stu-id="49eb6-132">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="49eb6-133">Bereitstellungstyp</span><span class="sxs-lookup"><span data-stu-id="49eb6-133">Deployment type</span></span>

<span data-ttu-id="49eb6-134">Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="49eb6-134">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="49eb6-135">SDK</span><span class="sxs-lookup"><span data-stu-id="49eb6-135">SDK</span></span>

<span data-ttu-id="49eb6-136">Geben Sie für einen Web-App-basierten Dienst, der die Razor Pages- oder MVC-Frameworks verwendet, das Web-SDK in der Projektdatei an:</span><span class="sxs-lookup"><span data-stu-id="49eb6-136">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="49eb6-137">Geben Sie das Worker-SDK in der Projektdatei an, wenn der Dienst nur Hintergrundaufgaben ausführt (z. B. [gehostete Dienste](xref:fundamentals/host/hosted-services)):</span><span class="sxs-lookup"><span data-stu-id="49eb6-137">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="49eb6-138">Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD)</span><span class="sxs-lookup"><span data-stu-id="49eb6-138">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="49eb6-139">Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem.</span><span class="sxs-lookup"><span data-stu-id="49eb6-139">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="49eb6-140">Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="49eb6-140">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="49eb6-141">Bei Verwendung des [Web-SDK](#sdk) ist eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="49eb6-141">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="49eb6-142">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-142">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="49eb6-143">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="49eb6-143">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="49eb6-144">Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="49eb6-144">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="49eb6-145">Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-145">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="49eb6-146">Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:</span><span class="sxs-lookup"><span data-stu-id="49eb6-146">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="49eb6-147">So führen Sie die Veröffentlichung für mehrere RIDs aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-147">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="49eb6-148">Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.</span><span class="sxs-lookup"><span data-stu-id="49eb6-148">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="49eb6-149">Verwenden Sie den Eigenschaftennamen [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).</span><span class="sxs-lookup"><span data-stu-id="49eb6-149">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="49eb6-150">Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="49eb6-150">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="49eb6-151">Benutzerkonten für Dienste</span><span class="sxs-lookup"><span data-stu-id="49eb6-151">Service user account</span></span>

<span data-ttu-id="49eb6-152">Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="49eb6-152">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="49eb6-153">Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:</span><span class="sxs-lookup"><span data-stu-id="49eb6-153">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="49eb6-154">Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="49eb6-154">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="49eb6-155">Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.</span><span class="sxs-lookup"><span data-stu-id="49eb6-155">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="49eb6-156">Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.</span><span class="sxs-lookup"><span data-stu-id="49eb6-156">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="49eb6-157">Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="49eb6-157">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="49eb6-158">Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.</span><span class="sxs-lookup"><span data-stu-id="49eb6-158">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="49eb6-159">Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="49eb6-159">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="49eb6-160">Rechte zum Anmelden als Dienst</span><span class="sxs-lookup"><span data-stu-id="49eb6-160">Log on as a service rights</span></span>

<span data-ttu-id="49eb6-161">Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-161">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="49eb6-162">Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-162">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="49eb6-163">Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-163">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="49eb6-164">Öffnen Sie die Richtlinie **Anmelden als Dienst**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-164">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="49eb6-165">Wählen Sie **Benutzer oder Gruppe hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-165">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="49eb6-166">Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-166">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="49eb6-167">Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-167">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="49eb6-168">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-168">Select **Advanced**.</span></span> <span data-ttu-id="49eb6-169">Klicken Sie auf **Find Now** (Suche starten).</span><span class="sxs-lookup"><span data-stu-id="49eb6-169">Select **Find Now**.</span></span> <span data-ttu-id="49eb6-170">Wählen Sie das betreffende Benutzerkonto aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-170">Select the user account from the list.</span></span> <span data-ttu-id="49eb6-171">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-171">Select **OK**.</span></span> <span data-ttu-id="49eb6-172">Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-172">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="49eb6-173">Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-173">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="49eb6-174">Erstellen und Verwalten des Windows-Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-174">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="49eb6-175">Erstellen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-175">Create a service</span></span>

<span data-ttu-id="49eb6-176">Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="49eb6-176">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="49eb6-177">Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-177">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = "{DOMAIN OR COMPUTER NAME\USER}", "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName "{EXE FILE PATH}" -Credential "{DOMAIN OR COMPUTER NAME\USER}" -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="49eb6-178">`{EXE PATH}`: entspricht dem Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-178">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="49eb6-179">Fügen Sie nicht die ausführbare Datei der App in den Pfad ein.</span><span class="sxs-lookup"><span data-stu-id="49eb6-179">Don't include the app's executable in the path.</span></span> <span data-ttu-id="49eb6-180">Außerdem benötigen Sie keinen nachstehenden Schrägstrich.</span><span class="sxs-lookup"><span data-stu-id="49eb6-180">A trailing slash isn't required.</span></span>
* <span data-ttu-id="49eb6-181">`{DOMAIN OR COMPUTER NAME\USER}`: entspricht dem Benutzerkonto für den Dienst (z. B. `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-181">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="49eb6-182">`{SERVICE NAME}`: entspricht dem Dienstnamen (z. B. `MyService`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-182">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="49eb6-183">`{EXE FILE PATH}`: entspricht dem Pfad zur ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-183">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="49eb6-184">Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-184">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="49eb6-185">`{DESCRIPTION}`: ist eine Beschreibung des Diensts (z. B. `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-185">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="49eb6-186">`{DISPLAY NAME}`: entspricht dem Anzeigenamen des Diensts (z. B. `My Service`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-186">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="49eb6-187">Starten eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-187">Start a service</span></span>

<span data-ttu-id="49eb6-188">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-188">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="49eb6-189">Das Starten des Diensts dauert ein paar Sekunden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-189">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="49eb6-190">Ermitteln des Status eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-190">Determine a service's status</span></span>

<span data-ttu-id="49eb6-191">Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:</span><span class="sxs-lookup"><span data-stu-id="49eb6-191">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="49eb6-192">Der Status wird als einer der folgenden Werte gemeldet:</span><span class="sxs-lookup"><span data-stu-id="49eb6-192">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="49eb6-193">Beenden eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-193">Stop a service</span></span>

<span data-ttu-id="49eb6-194">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-194">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="49eb6-195">Entfernen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-195">Remove a service</span></span>

<span data-ttu-id="49eb6-196">Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:</span><span class="sxs-lookup"><span data-stu-id="49eb6-196">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="49eb6-197">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="49eb6-197">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="49eb6-198">Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-198">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="49eb6-199">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="49eb6-199">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="49eb6-200">Konfigurieren von Endpunkten</span><span class="sxs-lookup"><span data-stu-id="49eb6-200">Configure endpoints</span></span>

<span data-ttu-id="49eb6-201">Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-201">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="49eb6-202">Konfigurieren Sie URL und Port, indem Sie die `ASPNETCORE_URLS`-Umgebungsvariable festlegen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-202">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="49eb6-203">Weitere Informationen zu den Konfigurationsansätzen für URLs und Ports finden Sie im relevanten Serverartikel:</span><span class="sxs-lookup"><span data-stu-id="49eb6-203">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="49eb6-204">Die vorangehende Anleitung behandelt die Unterstützung für HTTPS-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="49eb6-204">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="49eb6-205">Konfigurieren Sie z. B. die APP für HTTPS, wenn die Authentifizierung mit einem Windows-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-205">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="49eb6-206">Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-206">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="49eb6-207">Aktuelles Verzeichnis und Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="49eb6-207">Current directory and content root</span></span>

<span data-ttu-id="49eb6-208">Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-208">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="49eb6-209">Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).</span><span class="sxs-lookup"><span data-stu-id="49eb6-209">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="49eb6-210">Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-210">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="49eb6-211">Verwenden von ContentRootPath oder ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="49eb6-211">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="49eb6-212">Verwenden Sie [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) oder <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>, um die Ressourcen einer App zu finden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-212">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="49eb6-213">Wenn die App als Dienst ausgeführt wird, [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) durch <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> für <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-213">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="49eb6-214">Die Standardeinstellungsdateien *appsettings.json* und *appsettings.{Environment}.json* der App, werden über das Inhaltsstammverzeichnis der App geladen, indem [CreateDefaultBuilder](xref:fundamentals/host/generic-host#set-up-a-host) während der Hosterstellung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-214">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="49eb6-215">Für andere Einstellungsdateien, die in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> durch Entwicklercode geladen werden, muss <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> nicht aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-215">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="49eb6-216">Im folgenden Beispiel ist die Datei *custom_settings.json* im Inhaltsstammverzeichnis der App enthalten und wird geladen, ohne das ein Basispfad explizit festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="49eb6-216">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="49eb6-217">Versuchen Sie nicht, <xref:System.IO.Directory.GetCurrentDirectory*> zum Abrufen eines Ressourcenpfads zu verwenden, da eine Windows-Dienstanwendung den Ordner *C:\\WINDOWS\\system32* als aktuelles Verzeichnis zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-217">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="49eb6-218">Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger</span><span class="sxs-lookup"><span data-stu-id="49eb6-218">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="49eb6-219">Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="49eb6-219">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="49eb6-220">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="49eb6-220">Troubleshoot</span></span>

<span data-ttu-id="49eb6-221">Informationen zur Problembehandlung für Windows-Dienstanwendungen finden Sie unter <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="49eb6-221">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="49eb6-222">Häufige Fehler</span><span class="sxs-lookup"><span data-stu-id="49eb6-222">Common errors</span></span>

* <span data-ttu-id="49eb6-223">Eine alte oder eine Vorabversion von PowerShell wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="49eb6-223">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="49eb6-224">Der registrierte Dienst verwendet nicht die **veröffentlichte** Ausgabe des Befehls [dotnet publish](/dotnet/core/tools/dotnet-publish) der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="49eb6-224">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="49eb6-225">Die Ausgabe des Befehls [dotnet build](/dotnet/core/tools/dotnet-build) wird nicht für die Anwendungsbereitstellung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-225">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="49eb6-226">Veröffentlichte Ressourcen sind je nach Bereitstellungstyp in einem der folgenden Ordner vorzufinden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-226">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="49eb6-227">*bin/Release/{ZIELFRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="49eb6-227">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="49eb6-228">*bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="49eb6-228">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="49eb6-229">Der Dienst weist nicht den Status „WIRD AUSGEFÜHRT“ auf.</span><span class="sxs-lookup"><span data-stu-id="49eb6-229">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="49eb6-230">Die App enthält falsche Pfade zu Ressourcen (z. B. Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="49eb6-230">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="49eb6-231">Der Basispfad eines Windows-Diensts lautet: *C:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-231">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="49eb6-232">Der Benutzer verfügt nicht über die Berechtigung *Als Dienst anmelden*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-232">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="49eb6-233">Das Kennwort des Benutzers ist abgelaufen oder wird nicht ordnungsgemäß übermittelt, wenn der PowerShell-Befehl `New-Service` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-233">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="49eb6-234">Die App erfordert ASP.NET Core-Authentifizierung, aber sie wurde nicht für sichere (HTTPS-)Verbindungen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="49eb6-234">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="49eb6-235">Der Port für die Anforderungs-URL in der App ist fehlerhaft oder nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="49eb6-235">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="49eb6-236">System- und Anwendungsereignisprotokolle</span><span class="sxs-lookup"><span data-stu-id="49eb6-236">System and Application Event Logs</span></span>

<span data-ttu-id="49eb6-237">So greifen Sie auf die System- und Anwendungsereignisprotokolle zu:</span><span class="sxs-lookup"><span data-stu-id="49eb6-237">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="49eb6-238">Öffnen Sie das Startmenü, suchen Sie nach der *Ereignisanzeige*, und wählen Sie dann die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-238">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="49eb6-239">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-239">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="49eb6-240">Klicken Sie auf **System**, um das Systemereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-240">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="49eb6-241">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-241">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="49eb6-242">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-242">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="49eb6-243">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="49eb6-243">Run the app at a command prompt</span></span>

<span data-ttu-id="49eb6-244">Viele Startfehler erzeugen keine nützlichen Informationen in den Ereignisprotokollen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-244">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="49eb6-245">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-245">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="49eb6-246">Senken Sie den [Protokolliergrad](xref:fundamentals/logging/index#log-level), oder führen Sie die App in der [Entwicklungsumgebung](xref:fundamentals/environments) aus, um zusätzliche Informationen von der App zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="49eb6-246">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="49eb6-247">Bereinigen der Paketcaches</span><span class="sxs-lookup"><span data-stu-id="49eb6-247">Clear package caches</span></span>

<span data-ttu-id="49eb6-248">Eine funktionsfähige App kann direkt nach der Durchführung eines Upgrades für das .NET Core SDK auf dem Entwicklungscomputer oder einer Änderung der Paketversionen in der App fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-248">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="49eb6-249">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-249">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="49eb6-250">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-250">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="49eb6-251">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-251">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="49eb6-252">Bereinigen Sie die Paketcaches, indem Sie [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) über eine Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-252">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="49eb6-253">Sie können die Paketcaches auch mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und dem Befehl `nuget locals all -clear` bereinigen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-253">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="49eb6-254">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-254">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="49eb6-255">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-255">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="49eb6-256">Löschen Sie alle Dateien im Bereitstellungsordner auf dem Server, bevor Sie die App noch mal bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-256">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="49eb6-257">Langsame oder hängende App</span><span class="sxs-lookup"><span data-stu-id="49eb6-257">Slow or hanging app</span></span>

<span data-ttu-id="49eb6-258">Ein *Absturzabbild* ist eine Momentaufnahme des Systemarbeitsspeichers, die Ihnen dabei behilflich sein kann, die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="49eb6-258">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="49eb6-259">App stürzt ab, oder es tritt eine Ausnahme auf</span><span class="sxs-lookup"><span data-stu-id="49eb6-259">App crashes or encounters an exception</span></span>

<span data-ttu-id="49eb6-260">Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="49eb6-260">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="49eb6-261">Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="49eb6-261">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="49eb6-262">Führen Sie das PowerShell-Skript [EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) mit dem Namen des ausführbaren Anwendung aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-262">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="49eb6-263">Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-263">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="49eb6-264">Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-264">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1):</span></span>

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="49eb6-265">Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-265">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="49eb6-266">Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.</span><span class="sxs-lookup"><span data-stu-id="49eb6-266">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="49eb6-267">Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).</span><span class="sxs-lookup"><span data-stu-id="49eb6-267">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="49eb6-268">App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt</span><span class="sxs-lookup"><span data-stu-id="49eb6-268">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="49eb6-269">Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-269">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="49eb6-270">Analysieren des Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="49eb6-270">Analyze the dump</span></span>

<span data-ttu-id="49eb6-271">Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-271">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="49eb6-272">Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="49eb6-272">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49eb6-273">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="49eb6-273">Additional resources</span></span>

* <span data-ttu-id="49eb6-274">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="49eb6-274">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="49eb6-275">Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-275">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="49eb6-276">Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.</span><span class="sxs-lookup"><span data-stu-id="49eb6-276">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="49eb6-277">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49eb6-277">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49eb6-278">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="49eb6-278">Prerequisites</span></span>

* [<span data-ttu-id="49eb6-279">ASP.NET Core SDK 2.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="49eb6-279">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="49eb6-280">PowerShell 6.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="49eb6-280">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="49eb6-281">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="49eb6-281">App configuration</span></span>

<span data-ttu-id="49eb6-282">Die App benötigt Paketverweise für [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) und [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="49eb6-282">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="49eb6-283">Um bei der Ausführung außerhalb eines Dienstes zu testen und zu debuggen, fügen Sie Code hinzu, um festzustellen, ob die Anwendung als Dienst oder als Konsolenanwendung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-283">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="49eb6-284">Überprüfen Sie, ob der Debugger angefügt ist oder ob es einen `--console`-Parameter gibt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-284">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="49eb6-285">Wenn eine dieser Bedingungen erfüllt ist (die App wird nicht als Dienst ausgeführt), rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> auf.</span><span class="sxs-lookup"><span data-stu-id="49eb6-285">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="49eb6-286">Wenn die Bedingungen nicht zutreffen (die App als Dienst ausgeführt wird):</span><span class="sxs-lookup"><span data-stu-id="49eb6-286">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="49eb6-287">Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App.</span><span class="sxs-lookup"><span data-stu-id="49eb6-287">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="49eb6-288">Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn <xref:System.IO.Directory.GetCurrentDirectory*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-288">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="49eb6-289">Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="49eb6-289">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="49eb6-290">Dieser Schritt wird ausgeführt, bevor die App in `CreateWebHostBuilder` konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-290">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="49eb6-291">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-291">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="49eb6-292">Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der Parameter `--console` aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> diese empfängt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-292">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="49eb6-293">Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-293">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="49eb6-294">Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest.</span><span class="sxs-lookup"><span data-stu-id="49eb6-294">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="49eb6-295">Im folgenden Beispiel aus der Beispiel-App wird `RunAsCustomService` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aufgerufen, um Ereignisse in Bezug auf die Lebensdauer innerhalb der App zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="49eb6-295">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="49eb6-296">Weitere Informationen finden Sie im Abschnitt [Behandeln von Start- und Stopereignissen](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="49eb6-296">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="49eb6-297">Bereitstellungstyp</span><span class="sxs-lookup"><span data-stu-id="49eb6-297">Deployment type</span></span>

<span data-ttu-id="49eb6-298">Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="49eb6-298">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="49eb6-299">SDK</span><span class="sxs-lookup"><span data-stu-id="49eb6-299">SDK</span></span>

<span data-ttu-id="49eb6-300">Geben Sie für einen Web-App-basierten Dienst, der die Razor Pages- oder MVC-Frameworks verwendet, das Web-SDK in der Projektdatei an:</span><span class="sxs-lookup"><span data-stu-id="49eb6-300">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="49eb6-301">Geben Sie das Worker-SDK in der Projektdatei an, wenn der Dienst nur Hintergrundaufgaben ausführt (z. B. [gehostete Dienste](xref:fundamentals/host/hosted-services)):</span><span class="sxs-lookup"><span data-stu-id="49eb6-301">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="49eb6-302">Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD)</span><span class="sxs-lookup"><span data-stu-id="49eb6-302">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="49eb6-303">Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem.</span><span class="sxs-lookup"><span data-stu-id="49eb6-303">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="49eb6-304">Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="49eb6-304">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="49eb6-305">Der [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) enthält das Zielframework.</span><span class="sxs-lookup"><span data-stu-id="49eb6-305">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="49eb6-306">Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-306">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="49eb6-307">Die `<SelfContained>`-Eigenschaft ist auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-307">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="49eb6-308">Diese Eigenschaften geben dem SDK die Anweisung, eine ausführbare Datei ( *.exe*) für Windows sowie eine App zu generieren, die vom freigegebenen .NET Core-Framework abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="49eb6-308">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="49eb6-309">Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="49eb6-309">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="49eb6-310">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-310">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="49eb6-311">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="49eb6-311">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="49eb6-312">Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="49eb6-312">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="49eb6-313">Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-313">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="49eb6-314">Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:</span><span class="sxs-lookup"><span data-stu-id="49eb6-314">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="49eb6-315">So führen Sie die Veröffentlichung für mehrere RIDs aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-315">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="49eb6-316">Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.</span><span class="sxs-lookup"><span data-stu-id="49eb6-316">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="49eb6-317">Verwenden Sie den Eigenschaftennamen [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).</span><span class="sxs-lookup"><span data-stu-id="49eb6-317">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="49eb6-318">Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="49eb6-318">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="49eb6-319">Eine `<SelfContained>`-Eigenschaft wird auf `true` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="49eb6-319">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="49eb6-320">Benutzerkonten für Dienste</span><span class="sxs-lookup"><span data-stu-id="49eb6-320">Service user account</span></span>

<span data-ttu-id="49eb6-321">Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="49eb6-321">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="49eb6-322">Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:</span><span class="sxs-lookup"><span data-stu-id="49eb6-322">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="49eb6-323">Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="49eb6-323">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="49eb6-324">Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.</span><span class="sxs-lookup"><span data-stu-id="49eb6-324">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="49eb6-325">Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.</span><span class="sxs-lookup"><span data-stu-id="49eb6-325">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="49eb6-326">Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="49eb6-326">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="49eb6-327">Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.</span><span class="sxs-lookup"><span data-stu-id="49eb6-327">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="49eb6-328">Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="49eb6-328">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="49eb6-329">Rechte zum Anmelden als Dienst</span><span class="sxs-lookup"><span data-stu-id="49eb6-329">Log on as a service rights</span></span>

<span data-ttu-id="49eb6-330">Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-330">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="49eb6-331">Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-331">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="49eb6-332">Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-332">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="49eb6-333">Öffnen Sie die Richtlinie **Anmelden als Dienst**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-333">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="49eb6-334">Wählen Sie **Benutzer oder Gruppe hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-334">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="49eb6-335">Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-335">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="49eb6-336">Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-336">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="49eb6-337">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-337">Select **Advanced**.</span></span> <span data-ttu-id="49eb6-338">Klicken Sie auf **Find Now** (Suche starten).</span><span class="sxs-lookup"><span data-stu-id="49eb6-338">Select **Find Now**.</span></span> <span data-ttu-id="49eb6-339">Wählen Sie das betreffende Benutzerkonto aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-339">Select the user account from the list.</span></span> <span data-ttu-id="49eb6-340">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-340">Select **OK**.</span></span> <span data-ttu-id="49eb6-341">Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-341">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="49eb6-342">Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-342">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="49eb6-343">Erstellen und Verwalten des Windows-Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-343">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="49eb6-344">Erstellen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-344">Create a service</span></span>

<span data-ttu-id="49eb6-345">Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="49eb6-345">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="49eb6-346">Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-346">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="49eb6-347">`{EXE PATH}`: entspricht dem Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-347">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="49eb6-348">Fügen Sie nicht die ausführbare Datei der App in den Pfad ein.</span><span class="sxs-lookup"><span data-stu-id="49eb6-348">Don't include the app's executable in the path.</span></span> <span data-ttu-id="49eb6-349">Außerdem benötigen Sie keinen nachstehenden Schrägstrich.</span><span class="sxs-lookup"><span data-stu-id="49eb6-349">A trailing slash isn't required.</span></span>
* <span data-ttu-id="49eb6-350">`{DOMAIN OR COMPUTER NAME\USER}`: entspricht dem Benutzerkonto für den Dienst (z. B. `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-350">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="49eb6-351">`{SERVICE NAME}`: entspricht dem Dienstnamen (z. B. `MyService`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-351">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="49eb6-352">`{EXE FILE PATH}`: entspricht dem Pfad zur ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-352">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="49eb6-353">Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-353">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="49eb6-354">`{DESCRIPTION}`: ist eine Beschreibung des Diensts (z. B. `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-354">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="49eb6-355">`{DISPLAY NAME}`: entspricht dem Anzeigenamen des Diensts (z. B. `My Service`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-355">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="49eb6-356">Starten eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-356">Start a service</span></span>

<span data-ttu-id="49eb6-357">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-357">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="49eb6-358">Das Starten des Diensts dauert ein paar Sekunden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-358">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="49eb6-359">Ermitteln des Status eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-359">Determine a service's status</span></span>

<span data-ttu-id="49eb6-360">Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:</span><span class="sxs-lookup"><span data-stu-id="49eb6-360">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="49eb6-361">Der Status wird als einer der folgenden Werte gemeldet:</span><span class="sxs-lookup"><span data-stu-id="49eb6-361">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="49eb6-362">Beenden eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-362">Stop a service</span></span>

<span data-ttu-id="49eb6-363">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-363">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="49eb6-364">Entfernen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-364">Remove a service</span></span>

<span data-ttu-id="49eb6-365">Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:</span><span class="sxs-lookup"><span data-stu-id="49eb6-365">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="49eb6-366">Behandeln von Start- und Stopereignissen</span><span class="sxs-lookup"><span data-stu-id="49eb6-366">Handle starting and stopping events</span></span>

<span data-ttu-id="49eb6-367">Gehen Sie wie folgt vor, um <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>-, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>- und <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>-Ereignisse zu bearbeiten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-367">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="49eb6-368">Erstellen Sie eine von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> abgeleitete Klasse mit den `OnStarting`-, `OnStarted`- und `OnStopping`-Methoden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-368">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="49eb6-369">Erstellen Sie eine Erweiterungsmethode für <xref:Microsoft.AspNetCore.Hosting.IWebHost>, die den `CustomWebHostService` an <xref:System.ServiceProcess.ServiceBase.Run*> übergibt:</span><span class="sxs-lookup"><span data-stu-id="49eb6-369">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="49eb6-370">Rufen Sie in `Program.Main` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> die Erweiterungsmethode `RunAsCustomService` auf:</span><span class="sxs-lookup"><span data-stu-id="49eb6-370">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="49eb6-371">Wenn Sie Informationen dazu benötigen, wie Sie den Speicherort von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main` abrufen können, sehen Sie sich das Codebeispiel im Abschnitt [Bereitstellungstyp](#deployment-type) an.</span><span class="sxs-lookup"><span data-stu-id="49eb6-371">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="49eb6-372">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="49eb6-372">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="49eb6-373">Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-373">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="49eb6-374">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="49eb6-374">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="49eb6-375">Konfigurieren von Endpunkten</span><span class="sxs-lookup"><span data-stu-id="49eb6-375">Configure endpoints</span></span>

<span data-ttu-id="49eb6-376">Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-376">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="49eb6-377">Konfigurieren Sie URL und Port, indem Sie die `ASPNETCORE_URLS`-Umgebungsvariable festlegen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-377">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="49eb6-378">Weitere Informationen zu den Konfigurationsansätzen für URLs und Ports finden Sie im relevanten Serverartikel:</span><span class="sxs-lookup"><span data-stu-id="49eb6-378">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="49eb6-379">Die vorangehende Anleitung behandelt die Unterstützung für HTTPS-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="49eb6-379">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="49eb6-380">Konfigurieren Sie z. B. die APP für HTTPS, wenn die Authentifizierung mit einem Windows-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-380">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="49eb6-381">Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-381">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="49eb6-382">Aktuelles Verzeichnis und Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="49eb6-382">Current directory and content root</span></span>

<span data-ttu-id="49eb6-383">Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-383">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="49eb6-384">Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).</span><span class="sxs-lookup"><span data-stu-id="49eb6-384">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="49eb6-385">Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-385">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="49eb6-386">Legen Sie den Inhaltsstammpfad auf den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="49eb6-386">Set the content root path to the app's folder</span></span>

<span data-ttu-id="49eb6-387"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> entspricht dem Pfad, der für das Argument `binPath` bereitgestellt wird, wenn ein Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-387">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="49eb6-388">Anstatt `GetCurrentDirectory` aufzurufen, um Pfade zu Einstellungsdateien zu erstellen, rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> mit dem Pfad zum [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App auf.</span><span class="sxs-lookup"><span data-stu-id="49eb6-388">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="49eb6-389">Bestimmen Sie unter `Program.Main` den Pfad zum Ordner der ausführbaren Datei des Dienstes und verwenden Sie den Pfad, um das Inhaltsverzeichnis der App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="49eb6-389">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="49eb6-390">Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger</span><span class="sxs-lookup"><span data-stu-id="49eb6-390">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="49eb6-391">Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="49eb6-391">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="49eb6-392">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="49eb6-392">Troubleshoot</span></span>

<span data-ttu-id="49eb6-393">Informationen zur Problembehandlung für Windows-Dienstanwendungen finden Sie unter <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="49eb6-393">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="49eb6-394">Häufige Fehler</span><span class="sxs-lookup"><span data-stu-id="49eb6-394">Common errors</span></span>

* <span data-ttu-id="49eb6-395">Eine alte oder eine Vorabversion von PowerShell wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="49eb6-395">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="49eb6-396">Der registrierte Dienst verwendet nicht die **veröffentlichte** Ausgabe des Befehls [dotnet publish](/dotnet/core/tools/dotnet-publish) der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="49eb6-396">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="49eb6-397">Die Ausgabe des Befehls [dotnet build](/dotnet/core/tools/dotnet-build) wird nicht für die Anwendungsbereitstellung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-397">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="49eb6-398">Veröffentlichte Ressourcen sind je nach Bereitstellungstyp in einem der folgenden Ordner vorzufinden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-398">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="49eb6-399">*bin/Release/{ZIELFRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="49eb6-399">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="49eb6-400">*bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="49eb6-400">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="49eb6-401">Der Dienst weist nicht den Status „WIRD AUSGEFÜHRT“ auf.</span><span class="sxs-lookup"><span data-stu-id="49eb6-401">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="49eb6-402">Die App enthält falsche Pfade zu Ressourcen (z. B. Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="49eb6-402">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="49eb6-403">Der Basispfad eines Windows-Diensts lautet: *C:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-403">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="49eb6-404">Der Benutzer verfügt nicht über die Berechtigung *Als Dienst anmelden*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-404">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="49eb6-405">Das Kennwort des Benutzers ist abgelaufen oder wird nicht ordnungsgemäß übermittelt, wenn der PowerShell-Befehl `New-Service` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-405">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="49eb6-406">Die App erfordert ASP.NET Core-Authentifizierung, aber sie wurde nicht für sichere (HTTPS-)Verbindungen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="49eb6-406">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="49eb6-407">Der Port für die Anforderungs-URL in der App ist fehlerhaft oder nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="49eb6-407">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="49eb6-408">System- und Anwendungsereignisprotokolle</span><span class="sxs-lookup"><span data-stu-id="49eb6-408">System and Application Event Logs</span></span>

<span data-ttu-id="49eb6-409">So greifen Sie auf die System- und Anwendungsereignisprotokolle zu:</span><span class="sxs-lookup"><span data-stu-id="49eb6-409">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="49eb6-410">Öffnen Sie das Startmenü, suchen Sie nach der *Ereignisanzeige*, und wählen Sie dann die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-410">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="49eb6-411">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-411">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="49eb6-412">Klicken Sie auf **System**, um das Systemereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-412">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="49eb6-413">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-413">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="49eb6-414">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-414">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="49eb6-415">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="49eb6-415">Run the app at a command prompt</span></span>

<span data-ttu-id="49eb6-416">Viele Startfehler erzeugen keine nützlichen Informationen in den Ereignisprotokollen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-416">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="49eb6-417">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-417">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="49eb6-418">Senken Sie den [Protokolliergrad](xref:fundamentals/logging/index#log-level), oder führen Sie die App in der [Entwicklungsumgebung](xref:fundamentals/environments) aus, um zusätzliche Informationen von der App zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="49eb6-418">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="49eb6-419">Bereinigen der Paketcaches</span><span class="sxs-lookup"><span data-stu-id="49eb6-419">Clear package caches</span></span>

<span data-ttu-id="49eb6-420">Eine funktionsfähige App kann direkt nach der Durchführung eines Upgrades für das .NET Core SDK auf dem Entwicklungscomputer oder einer Änderung der Paketversionen in der App fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-420">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="49eb6-421">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-421">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="49eb6-422">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-422">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="49eb6-423">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-423">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="49eb6-424">Bereinigen Sie die Paketcaches, indem Sie [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) über eine Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-424">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="49eb6-425">Sie können die Paketcaches auch mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und dem Befehl `nuget locals all -clear` bereinigen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-425">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="49eb6-426">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-426">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="49eb6-427">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-427">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="49eb6-428">Löschen Sie alle Dateien im Bereitstellungsordner auf dem Server, bevor Sie die App noch mal bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-428">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="49eb6-429">Langsame oder hängende App</span><span class="sxs-lookup"><span data-stu-id="49eb6-429">Slow or hanging app</span></span>

<span data-ttu-id="49eb6-430">Ein *Absturzabbild* ist eine Momentaufnahme des Systemarbeitsspeichers, die Ihnen dabei behilflich sein kann, die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="49eb6-430">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="49eb6-431">App stürzt ab, oder es tritt eine Ausnahme auf</span><span class="sxs-lookup"><span data-stu-id="49eb6-431">App crashes or encounters an exception</span></span>

<span data-ttu-id="49eb6-432">Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="49eb6-432">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="49eb6-433">Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="49eb6-433">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="49eb6-434">Führen Sie das PowerShell-Skript [EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) mit dem Namen des ausführbaren Anwendung aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-434">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="49eb6-435">Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-435">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="49eb6-436">Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-436">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="49eb6-437">Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-437">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="49eb6-438">Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.</span><span class="sxs-lookup"><span data-stu-id="49eb6-438">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="49eb6-439">Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).</span><span class="sxs-lookup"><span data-stu-id="49eb6-439">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="49eb6-440">App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt</span><span class="sxs-lookup"><span data-stu-id="49eb6-440">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="49eb6-441">Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-441">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="49eb6-442">Analysieren des Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="49eb6-442">Analyze the dump</span></span>

<span data-ttu-id="49eb6-443">Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-443">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="49eb6-444">Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="49eb6-444">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49eb6-445">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="49eb6-445">Additional resources</span></span>

* <span data-ttu-id="49eb6-446">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="49eb6-446">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="49eb6-447">Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-447">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="49eb6-448">Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.</span><span class="sxs-lookup"><span data-stu-id="49eb6-448">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="49eb6-449">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="49eb6-449">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49eb6-450">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="49eb6-450">Prerequisites</span></span>

* [<span data-ttu-id="49eb6-451">ASP.NET Core SDK 2.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="49eb6-451">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="49eb6-452">PowerShell 6.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="49eb6-452">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="49eb6-453">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="49eb6-453">App configuration</span></span>

<span data-ttu-id="49eb6-454">Die App benötigt Paketverweise für [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) und [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="49eb6-454">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="49eb6-455">Um bei der Ausführung außerhalb eines Dienstes zu testen und zu debuggen, fügen Sie Code hinzu, um festzustellen, ob die Anwendung als Dienst oder als Konsolenanwendung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-455">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="49eb6-456">Überprüfen Sie, ob der Debugger angefügt ist oder ob es einen `--console`-Parameter gibt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-456">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="49eb6-457">Wenn eine dieser Bedingungen erfüllt ist (die App wird nicht als Dienst ausgeführt), rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> auf.</span><span class="sxs-lookup"><span data-stu-id="49eb6-457">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="49eb6-458">Wenn die Bedingungen nicht zutreffen (die App als Dienst ausgeführt wird):</span><span class="sxs-lookup"><span data-stu-id="49eb6-458">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="49eb6-459">Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App.</span><span class="sxs-lookup"><span data-stu-id="49eb6-459">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="49eb6-460">Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn <xref:System.IO.Directory.GetCurrentDirectory*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-460">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="49eb6-461">Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="49eb6-461">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="49eb6-462">Dieser Schritt wird ausgeführt, bevor die App in `CreateWebHostBuilder` konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-462">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="49eb6-463">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-463">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="49eb6-464">Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der Parameter `--console` aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> diese empfängt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-464">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="49eb6-465">Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-465">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="49eb6-466">Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest.</span><span class="sxs-lookup"><span data-stu-id="49eb6-466">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="49eb6-467">Im folgenden Beispiel aus der Beispiel-App wird `RunAsCustomService` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aufgerufen, um Ereignisse in Bezug auf die Lebensdauer innerhalb der App zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="49eb6-467">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="49eb6-468">Weitere Informationen finden Sie im Abschnitt [Behandeln von Start- und Stopereignissen](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="49eb6-468">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="49eb6-469">Bereitstellungstyp</span><span class="sxs-lookup"><span data-stu-id="49eb6-469">Deployment type</span></span>

<span data-ttu-id="49eb6-470">Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="49eb6-470">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="49eb6-471">SDK</span><span class="sxs-lookup"><span data-stu-id="49eb6-471">SDK</span></span>

<span data-ttu-id="49eb6-472">Geben Sie für einen Web-App-basierten Dienst, der die Razor Pages- oder MVC-Frameworks verwendet, das Web-SDK in der Projektdatei an:</span><span class="sxs-lookup"><span data-stu-id="49eb6-472">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="49eb6-473">Geben Sie das Worker-SDK in der Projektdatei an, wenn der Dienst nur Hintergrundaufgaben ausführt (z. B. [gehostete Dienste](xref:fundamentals/host/hosted-services)):</span><span class="sxs-lookup"><span data-stu-id="49eb6-473">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="49eb6-474">Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD)</span><span class="sxs-lookup"><span data-stu-id="49eb6-474">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="49eb6-475">Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem.</span><span class="sxs-lookup"><span data-stu-id="49eb6-475">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="49eb6-476">Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="49eb6-476">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="49eb6-477">Der [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) enthält das Zielframework.</span><span class="sxs-lookup"><span data-stu-id="49eb6-477">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="49eb6-478">Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-478">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="49eb6-479">Die `<SelfContained>`-Eigenschaft ist auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-479">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="49eb6-480">Diese Eigenschaften geben dem SDK die Anweisung, eine ausführbare Datei ( *.exe*) für Windows sowie eine App zu generieren, die vom freigegebenen .NET Core-Framework abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="49eb6-480">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="49eb6-481">Die `<UseAppHost>`-Eigenschaft ist auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-481">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="49eb6-482">Diese Eigenschaft stellt für den Dienst einen Aktivierungspfad (eine ausführbare Datei, *EXE*) für eine frameworkabhängige Bereitstellung bereit.</span><span class="sxs-lookup"><span data-stu-id="49eb6-482">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="49eb6-483">Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="49eb6-483">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="49eb6-484">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-484">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="49eb6-485">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="49eb6-485">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="49eb6-486">Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="49eb6-486">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="49eb6-487">Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-487">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="49eb6-488">Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:</span><span class="sxs-lookup"><span data-stu-id="49eb6-488">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="49eb6-489">So führen Sie die Veröffentlichung für mehrere RIDs aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-489">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="49eb6-490">Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.</span><span class="sxs-lookup"><span data-stu-id="49eb6-490">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="49eb6-491">Verwenden Sie den Eigenschaftennamen [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).</span><span class="sxs-lookup"><span data-stu-id="49eb6-491">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="49eb6-492">Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="49eb6-492">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="49eb6-493">Eine `<SelfContained>`-Eigenschaft wird auf `true` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="49eb6-493">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="49eb6-494">Benutzerkonten für Dienste</span><span class="sxs-lookup"><span data-stu-id="49eb6-494">Service user account</span></span>

<span data-ttu-id="49eb6-495">Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="49eb6-495">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="49eb6-496">Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:</span><span class="sxs-lookup"><span data-stu-id="49eb6-496">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="49eb6-497">Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="49eb6-497">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="49eb6-498">Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.</span><span class="sxs-lookup"><span data-stu-id="49eb6-498">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="49eb6-499">Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.</span><span class="sxs-lookup"><span data-stu-id="49eb6-499">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="49eb6-500">Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="49eb6-500">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="49eb6-501">Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.</span><span class="sxs-lookup"><span data-stu-id="49eb6-501">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="49eb6-502">Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="49eb6-502">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="49eb6-503">Rechte zum Anmelden als Dienst</span><span class="sxs-lookup"><span data-stu-id="49eb6-503">Log on as a service rights</span></span>

<span data-ttu-id="49eb6-504">Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-504">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="49eb6-505">Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-505">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="49eb6-506">Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-506">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="49eb6-507">Öffnen Sie die Richtlinie **Anmelden als Dienst**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-507">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="49eb6-508">Wählen Sie **Benutzer oder Gruppe hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-508">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="49eb6-509">Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-509">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="49eb6-510">Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-510">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="49eb6-511">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-511">Select **Advanced**.</span></span> <span data-ttu-id="49eb6-512">Klicken Sie auf **Find Now** (Suche starten).</span><span class="sxs-lookup"><span data-stu-id="49eb6-512">Select **Find Now**.</span></span> <span data-ttu-id="49eb6-513">Wählen Sie das betreffende Benutzerkonto aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-513">Select the user account from the list.</span></span> <span data-ttu-id="49eb6-514">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-514">Select **OK**.</span></span> <span data-ttu-id="49eb6-515">Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-515">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="49eb6-516">Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-516">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="49eb6-517">Erstellen und Verwalten des Windows-Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-517">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="49eb6-518">Erstellen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-518">Create a service</span></span>

<span data-ttu-id="49eb6-519">Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="49eb6-519">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="49eb6-520">Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-520">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="49eb6-521">`{EXE PATH}`: entspricht dem Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-521">`{EXE PATH}`: Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="49eb6-522">Fügen Sie nicht die ausführbare Datei der App in den Pfad ein.</span><span class="sxs-lookup"><span data-stu-id="49eb6-522">Don't include the app's executable in the path.</span></span> <span data-ttu-id="49eb6-523">Außerdem benötigen Sie keinen nachstehenden Schrägstrich.</span><span class="sxs-lookup"><span data-stu-id="49eb6-523">A trailing slash isn't required.</span></span>
* <span data-ttu-id="49eb6-524">`{DOMAIN OR COMPUTER NAME\USER}`: entspricht dem Benutzerkonto für den Dienst (z. B. `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-524">`{DOMAIN OR COMPUTER NAME\USER}`: Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="49eb6-525">`{SERVICE NAME}`: entspricht dem Dienstnamen (z. B. `MyService`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-525">`{SERVICE NAME}`: Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="49eb6-526">`{EXE FILE PATH}`: entspricht dem Pfad zur ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-526">`{EXE FILE PATH}`: The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="49eb6-527">Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-527">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="49eb6-528">`{DESCRIPTION}`: ist eine Beschreibung des Diensts (z. B. `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-528">`{DESCRIPTION}`: Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="49eb6-529">`{DISPLAY NAME}`: entspricht dem Anzeigenamen des Diensts (z. B. `My Service`).</span><span class="sxs-lookup"><span data-stu-id="49eb6-529">`{DISPLAY NAME}`: Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="49eb6-530">Starten eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-530">Start a service</span></span>

<span data-ttu-id="49eb6-531">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-531">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="49eb6-532">Das Starten des Diensts dauert ein paar Sekunden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-532">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="49eb6-533">Ermitteln des Status eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-533">Determine a service's status</span></span>

<span data-ttu-id="49eb6-534">Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:</span><span class="sxs-lookup"><span data-stu-id="49eb6-534">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="49eb6-535">Der Status wird als einer der folgenden Werte gemeldet:</span><span class="sxs-lookup"><span data-stu-id="49eb6-535">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="49eb6-536">Beenden eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-536">Stop a service</span></span>

<span data-ttu-id="49eb6-537">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-537">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="49eb6-538">Entfernen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="49eb6-538">Remove a service</span></span>

<span data-ttu-id="49eb6-539">Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:</span><span class="sxs-lookup"><span data-stu-id="49eb6-539">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="49eb6-540">Behandeln von Start- und Stopereignissen</span><span class="sxs-lookup"><span data-stu-id="49eb6-540">Handle starting and stopping events</span></span>

<span data-ttu-id="49eb6-541">Gehen Sie wie folgt vor, um <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>-, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>- und <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>-Ereignisse zu bearbeiten:</span><span class="sxs-lookup"><span data-stu-id="49eb6-541">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="49eb6-542">Erstellen Sie eine von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> abgeleitete Klasse mit den `OnStarting`-, `OnStarted`- und `OnStopping`-Methoden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-542">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="49eb6-543">Erstellen Sie eine Erweiterungsmethode für <xref:Microsoft.AspNetCore.Hosting.IWebHost>, die den `CustomWebHostService` an <xref:System.ServiceProcess.ServiceBase.Run*> übergibt:</span><span class="sxs-lookup"><span data-stu-id="49eb6-543">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="49eb6-544">Rufen Sie in `Program.Main` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> die Erweiterungsmethode `RunAsCustomService` auf:</span><span class="sxs-lookup"><span data-stu-id="49eb6-544">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="49eb6-545">Wenn Sie Informationen dazu benötigen, wie Sie den Speicherort von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main` abrufen können, sehen Sie sich das Codebeispiel im Abschnitt [Bereitstellungstyp](#deployment-type) an.</span><span class="sxs-lookup"><span data-stu-id="49eb6-545">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="49eb6-546">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="49eb6-546">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="49eb6-547">Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-547">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="49eb6-548">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="49eb6-548">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="49eb6-549">Konfigurieren von Endpunkten</span><span class="sxs-lookup"><span data-stu-id="49eb6-549">Configure endpoints</span></span>

<span data-ttu-id="49eb6-550">Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-550">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="49eb6-551">Konfigurieren Sie URL und Port, indem Sie die `ASPNETCORE_URLS`-Umgebungsvariable festlegen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-551">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="49eb6-552">Weitere Informationen zu den Konfigurationsansätzen für URLs und Ports finden Sie im relevanten Serverartikel:</span><span class="sxs-lookup"><span data-stu-id="49eb6-552">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="49eb6-553">Die vorangehende Anleitung behandelt die Unterstützung für HTTPS-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="49eb6-553">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="49eb6-554">Konfigurieren Sie z. B. die APP für HTTPS, wenn die Authentifizierung mit einem Windows-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-554">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="49eb6-555">Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-555">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="49eb6-556">Aktuelles Verzeichnis und Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="49eb6-556">Current directory and content root</span></span>

<span data-ttu-id="49eb6-557">Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-557">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="49eb6-558">Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).</span><span class="sxs-lookup"><span data-stu-id="49eb6-558">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="49eb6-559">Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-559">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="49eb6-560">Legen Sie den Inhaltsstammpfad auf den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="49eb6-560">Set the content root path to the app's folder</span></span>

<span data-ttu-id="49eb6-561"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> entspricht dem Pfad, der für das Argument `binPath` bereitgestellt wird, wenn ein Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-561">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="49eb6-562">Anstatt `GetCurrentDirectory` aufzurufen, um Pfade zu Einstellungsdateien zu erstellen, rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> mit dem Pfad zum [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App auf.</span><span class="sxs-lookup"><span data-stu-id="49eb6-562">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="49eb6-563">Bestimmen Sie unter `Program.Main` den Pfad zum Ordner der ausführbaren Datei des Dienstes und verwenden Sie den Pfad, um das Inhaltsverzeichnis der App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="49eb6-563">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="49eb6-564">Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger</span><span class="sxs-lookup"><span data-stu-id="49eb6-564">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="49eb6-565">Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="49eb6-565">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="49eb6-566">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="49eb6-566">Troubleshoot</span></span>

<span data-ttu-id="49eb6-567">Informationen zur Problembehandlung für Windows-Dienstanwendungen finden Sie unter <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="49eb6-567">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="49eb6-568">Häufige Fehler</span><span class="sxs-lookup"><span data-stu-id="49eb6-568">Common errors</span></span>

* <span data-ttu-id="49eb6-569">Eine alte oder eine Vorabversion von PowerShell wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="49eb6-569">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="49eb6-570">Der registrierte Dienst verwendet nicht die **veröffentlichte** Ausgabe des Befehls [dotnet publish](/dotnet/core/tools/dotnet-publish) der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="49eb6-570">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="49eb6-571">Die Ausgabe des Befehls [dotnet build](/dotnet/core/tools/dotnet-build) wird nicht für die Anwendungsbereitstellung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-571">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="49eb6-572">Veröffentlichte Ressourcen sind je nach Bereitstellungstyp in einem der folgenden Ordner vorzufinden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-572">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="49eb6-573">*bin/Release/{ZIELFRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="49eb6-573">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="49eb6-574">*bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="49eb6-574">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="49eb6-575">Der Dienst weist nicht den Status „WIRD AUSGEFÜHRT“ auf.</span><span class="sxs-lookup"><span data-stu-id="49eb6-575">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="49eb6-576">Die App enthält falsche Pfade zu Ressourcen (z. B. Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="49eb6-576">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="49eb6-577">Der Basispfad eines Windows-Diensts lautet: *C:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-577">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="49eb6-578">Der Benutzer verfügt nicht über die Berechtigung *Als Dienst anmelden*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-578">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="49eb6-579">Das Kennwort des Benutzers ist abgelaufen oder wird nicht ordnungsgemäß übermittelt, wenn der PowerShell-Befehl `New-Service` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="49eb6-579">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="49eb6-580">Die App erfordert ASP.NET Core-Authentifizierung, aber sie wurde nicht für sichere (HTTPS-)Verbindungen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="49eb6-580">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="49eb6-581">Der Port für die Anforderungs-URL in der App ist fehlerhaft oder nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="49eb6-581">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="49eb6-582">System- und Anwendungsereignisprotokolle</span><span class="sxs-lookup"><span data-stu-id="49eb6-582">System and Application Event Logs</span></span>

<span data-ttu-id="49eb6-583">So greifen Sie auf die System- und Anwendungsereignisprotokolle zu:</span><span class="sxs-lookup"><span data-stu-id="49eb6-583">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="49eb6-584">Öffnen Sie das Startmenü, suchen Sie nach der *Ereignisanzeige*, und wählen Sie dann die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="49eb6-584">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="49eb6-585">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="49eb6-585">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="49eb6-586">Klicken Sie auf **System**, um das Systemereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-586">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="49eb6-587">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-587">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="49eb6-588">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-588">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="49eb6-589">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="49eb6-589">Run the app at a command prompt</span></span>

<span data-ttu-id="49eb6-590">Viele Startfehler erzeugen keine nützlichen Informationen in den Ereignisprotokollen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-590">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="49eb6-591">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-591">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="49eb6-592">Senken Sie den [Protokolliergrad](xref:fundamentals/logging/index#log-level), oder führen Sie die App in der [Entwicklungsumgebung](xref:fundamentals/environments) aus, um zusätzliche Informationen von der App zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="49eb6-592">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="49eb6-593">Bereinigen der Paketcaches</span><span class="sxs-lookup"><span data-stu-id="49eb6-593">Clear package caches</span></span>

<span data-ttu-id="49eb6-594">Eine funktionsfähige App kann direkt nach der Durchführung eines Upgrades für das .NET Core SDK auf dem Entwicklungscomputer oder einer Änderung der Paketversionen in der App fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-594">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="49eb6-595">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-595">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="49eb6-596">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="49eb6-596">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="49eb6-597">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="49eb6-597">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="49eb6-598">Bereinigen Sie die Paketcaches, indem Sie [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) über eine Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-598">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="49eb6-599">Sie können die Paketcaches auch mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und dem Befehl `nuget locals all -clear` bereinigen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-599">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="49eb6-600">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-600">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="49eb6-601">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="49eb6-601">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="49eb6-602">Löschen Sie alle Dateien im Bereitstellungsordner auf dem Server, bevor Sie die App noch mal bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-602">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="49eb6-603">Langsame oder hängende App</span><span class="sxs-lookup"><span data-stu-id="49eb6-603">Slow or hanging app</span></span>

<span data-ttu-id="49eb6-604">Ein *Absturzabbild* ist eine Momentaufnahme des Systemarbeitsspeichers, die Ihnen dabei behilflich sein kann, die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="49eb6-604">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="49eb6-605">App stürzt ab, oder es tritt eine Ausnahme auf</span><span class="sxs-lookup"><span data-stu-id="49eb6-605">App crashes or encounters an exception</span></span>

<span data-ttu-id="49eb6-606">Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="49eb6-606">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="49eb6-607">Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="49eb6-607">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="49eb6-608">Führen Sie das PowerShell-Skript [EnableDumps](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) mit dem Namen des ausführbaren Anwendung aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-608">Run the [EnableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="49eb6-609">Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.</span><span class="sxs-lookup"><span data-stu-id="49eb6-609">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="49eb6-610">Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="49eb6-610">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="49eb6-611">Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-611">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="49eb6-612">Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.</span><span class="sxs-lookup"><span data-stu-id="49eb6-612">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="49eb6-613">Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).</span><span class="sxs-lookup"><span data-stu-id="49eb6-613">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="49eb6-614">App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt</span><span class="sxs-lookup"><span data-stu-id="49eb6-614">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="49eb6-615">Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="49eb6-615">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="49eb6-616">Analysieren des Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="49eb6-616">Analyze the dump</span></span>

<span data-ttu-id="49eb6-617">Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="49eb6-617">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="49eb6-618">Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="49eb6-618">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49eb6-619">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="49eb6-619">Additional resources</span></span>

* <span data-ttu-id="49eb6-620">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="49eb6-620">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
