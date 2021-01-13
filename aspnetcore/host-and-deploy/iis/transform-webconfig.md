---
title: Transformieren von web.config
author: rick-anderson
description: Erfahren Sie, wie Sie die Datei „web.config“ beim Veröffentlichen einer ASP.NET Core-App transformieren.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/transform-webconfig
ms.openlocfilehash: d264aaee7889ec1c8ee0fe6b1f52ccc4cf355745
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854625"
---
# <a name="transform-webconfig"></a><span data-ttu-id="eb1d2-103">Transformieren von web.config</span><span class="sxs-lookup"><span data-stu-id="eb1d2-103">Transform web.config</span></span>

<span data-ttu-id="eb1d2-104">Von [Vijay Ramakrishnan](https://github.com/vijayrkn)</span><span class="sxs-lookup"><span data-stu-id="eb1d2-104">By [Vijay Ramakrishnan](https://github.com/vijayrkn)</span></span>

<span data-ttu-id="eb1d2-105">Transformationen der *web.config*-Datei können automatisch angewendet werden, wenn eine App veröffentlicht wird basierend auf:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-105">Transformations to the *web.config* file can be applied automatically when an app is published based on:</span></span>

* [<span data-ttu-id="eb1d2-106">Buildkonfiguration</span><span class="sxs-lookup"><span data-stu-id="eb1d2-106">Build configuration</span></span>](#build-configuration)
* [<span data-ttu-id="eb1d2-107">Profil</span><span class="sxs-lookup"><span data-stu-id="eb1d2-107">Profile</span></span>](#profile)
* [<span data-ttu-id="eb1d2-108">Umgebung</span><span class="sxs-lookup"><span data-stu-id="eb1d2-108">Environment</span></span>](#environment)
* [<span data-ttu-id="eb1d2-109">Benutzerdefiniert</span><span class="sxs-lookup"><span data-stu-id="eb1d2-109">Custom</span></span>](#custom)

<span data-ttu-id="eb1d2-110">Diese Transformationen kommen bei den folgenden *web.config*-Generierungsszenarien vor:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-110">These transformations occur for either of the following *web.config* generation scenarios:</span></span>

* <span data-ttu-id="eb1d2-111">Automatisch generiert vom `Microsoft.NET.Sdk.Web`-SDK.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-111">Generated automatically by the `Microsoft.NET.Sdk.Web` SDK.</span></span>
* <span data-ttu-id="eb1d2-112">Durch den Entwickler im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-112">Provided by the developer in the [content root](xref:fundamentals/index#content-root) of the app.</span></span>

## <a name="build-configuration"></a><span data-ttu-id="eb1d2-113">Buildkonfiguration</span><span class="sxs-lookup"><span data-stu-id="eb1d2-113">Build configuration</span></span>

<span data-ttu-id="eb1d2-114">Transformationen der Buildkonfiguration werden zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-114">Build configuration transforms are run first.</span></span>

<span data-ttu-id="eb1d2-115">Beziehen Sie eine *web.{KONFIGURATION}.config*-Datei für jede [Buildkonfiguration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) ein, die eine *web.config*-Transformation erfordert.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-115">Include a *web.{CONFIGURATION}.config* file for each [build configuration (Debug|Release)](/dotnet/core/tools/dotnet-publish#options) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="eb1d2-116">Im folgenden Beispiel wird eine konfigurationsspezifische Umgebungsvariable in *web.Release.config* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-116">In the following example, a configuration-specific environment variable is set in *web.Release.config*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Configuration_Specific" 
                               value="Configuration_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="eb1d2-117">Die Transformation wird angewendet, wenn die Konfiguration auf *Release* festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-117">The transform is applied when the configuration is set to *Release*:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="eb1d2-118">Die MSBuild-Eigenschaft für die Konfiguration ist `$(Configuration)`.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-118">The MSBuild property for the configuration is `$(Configuration)`.</span></span>

## <a name="profile"></a><span data-ttu-id="eb1d2-119">Profile</span><span class="sxs-lookup"><span data-stu-id="eb1d2-119">Profile</span></span>

<span data-ttu-id="eb1d2-120">Profiltransformationen werden als Zweites nach den Transformationen der [Buildkonfiguration](#build-configuration) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-120">Profile transformations are run second, after [Build configuration](#build-configuration) transforms.</span></span>

<span data-ttu-id="eb1d2-121">Beziehen Sie eine *web.{PROFIL}.config*-Datei für jede Profilkonfiguration ein, die eine *web.config*-Transformation erfordert.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-121">Include a *web.{PROFILE}.config* file for each profile configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="eb1d2-122">Im folgenden Beispiel wird eine profilspezifische Umgebungsvariable in *web.FolderProfile.config* für ein Ordnerveröffentlichungsprofil festgelegt:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-122">In the following example, a profile-specific environment variable is set in *web.FolderProfile.config* for a folder publish profile:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Profile_Specific" 
                               value="Profile_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="eb1d2-123">Die Transformation wird bei dem Profil *FolderProfile* angewendet:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-123">The transform is applied when the profile is *FolderProfile*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:PublishProfile=FolderProfile
```

<span data-ttu-id="eb1d2-124">Die MSBuild-Eigenschaft für den Profilnamen ist `$(PublishProfile)`.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-124">The MSBuild property for the profile name is `$(PublishProfile)`.</span></span>

<span data-ttu-id="eb1d2-125">Wenn kein Profil übergeben wird, lautet der Standardname für das Profil **FileSystem**, und *web.FileSystem.config* wird angewendet, wenn die Datei im Inhaltsstammverzeichnis der App vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-125">If no profile is passed, the default profile name is **FileSystem** and *web.FileSystem.config* is applied if the file is present in the app's content root.</span></span>

## <a name="environment"></a><span data-ttu-id="eb1d2-126">Umgebung</span><span class="sxs-lookup"><span data-stu-id="eb1d2-126">Environment</span></span>

<span data-ttu-id="eb1d2-127">Umgebungstransformationen werden als Drittes nach den Transformationen von [Buildkonfiguration](#build-configuration) und [Profil](#profile) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-127">Environment transformations are run third, after [Build configuration](#build-configuration) and [Profile](#profile) transforms.</span></span>

<span data-ttu-id="eb1d2-128">Beziehen Sie eine *web.{UMGEBUNG}.config*-Datei für jede [Umgebung](xref:fundamentals/environments) ein, die eine *web.config*-Transformation erfordert.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-128">Include a *web.{ENVIRONMENT}.config* file for each [environment](xref:fundamentals/environments) requiring a *web.config* transformation.</span></span>

<span data-ttu-id="eb1d2-129">Im folgenden Beispiel wird eine umgebungsspezifische Umgebungsvariable in *web.Production.config* für die Produktionsumgebung festgelegt:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-129">In the following example, an environment-specific environment variable is set in *web.Production.config* for the Production environment:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Environment_Specific" 
                               value="Environment_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="eb1d2-130">Die Transformation wird angewendet, wenn die Umgebung *Production* ist:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-130">The transform is applied when the environment is *Production*:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:EnvironmentName=Production
```

<span data-ttu-id="eb1d2-131">Die MSBuild-Eigenschaft für die Umgebung ist `$(EnvironmentName)`.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-131">The MSBuild property for the environment is `$(EnvironmentName)`.</span></span>

<span data-ttu-id="eb1d2-132">Informationen zum Veröffentlichen von Visual Studio aus und Verwenden eines Veröffentlichungsprofils finden Sie unter <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-132">When publishing from Visual Studio and using a publish profile, see <xref:host-and-deploy/visual-studio-publish-profiles#set-the-environment>.</span></span>

<span data-ttu-id="eb1d2-133">Die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable wird automatisch der *web.config*-Datei hinzugefügt, wenn der Name der Umgebung angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-133">The `ASPNETCORE_ENVIRONMENT` environment variable is automatically added to the *web.config* file when the environment name is specified.</span></span>

## <a name="custom"></a><span data-ttu-id="eb1d2-134">Benutzerdefiniert</span><span class="sxs-lookup"><span data-stu-id="eb1d2-134">Custom</span></span>

<span data-ttu-id="eb1d2-135">Benutzerdefinierte Transformationen werden als Letztes nach den Transformationen von [Buildkonfiguration](#build-configuration), [Profil](#profile) und [Umgebung](#environment) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-135">Custom transformations are run last, after [Build configuration](#build-configuration), [Profile](#profile), and [Environment](#environment) transforms.</span></span>

<span data-ttu-id="eb1d2-136">Beziehen Sie eine *{BENUTZERDEFINIERTER_NAME}.transform*-Datei für jede benutzerdefinierte Konfiguration ein, die eine *web.config*-Transformation erfordert.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-136">Include a *{CUSTOM_NAME}.transform* file for each custom configuration requiring a *web.config* transformation.</span></span>

<span data-ttu-id="eb1d2-137">Im folgenden Beispiel wird eine benutzerdefinierte Transformationsumgebungsvariable in *custom.transform* festgelegt:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-137">In the following example, a custom transform environment variable is set in *custom.transform*:</span></span>

```xml
<?xml version="1.0"?>
<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  <location>
    <system.webServer>
      <aspNetCore>
        <environmentVariables xdt:Transform="InsertIfMissing">
          <environmentVariable name="Custom_Specific" 
                               value="Custom_Specific_Value" 
                               xdt:Locator="Match(name)" 
                               xdt:Transform="InsertIfMissing" />
        </environmentVariables>
      </aspNetCore>
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="eb1d2-138">Die Transformation wird angewendet, wenn die `CustomTransformFileName`-Eigenschaft dem [dotnet publish](/dotnet/core/tools/dotnet-publish)-Befehl übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-138">The transform is applied when the `CustomTransformFileName` property is passed to the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

```dotnetcli
dotnet publish --configuration Release /p:CustomTransformFileName=custom.transform
```

<span data-ttu-id="eb1d2-139">Die MSBuild-Eigenschaft für den Profilnamen ist `$(CustomTransformFileName)`.</span><span class="sxs-lookup"><span data-stu-id="eb1d2-139">The MSBuild property for the profile name is `$(CustomTransformFileName)`.</span></span>

## <a name="prevent-webconfig-transformation"></a><span data-ttu-id="eb1d2-140">Verhindern der web.config-Transformation</span><span class="sxs-lookup"><span data-stu-id="eb1d2-140">Prevent web.config transformation</span></span>

<span data-ttu-id="eb1d2-141">Um Transformationen der *web.config*-Datei zu verhindern, legen Sie die MSBuild-Eigenschaft `$(IsWebConfigTransformDisabled)` fest:</span><span class="sxs-lookup"><span data-stu-id="eb1d2-141">To prevent transformations of the *web.config* file, set the MSBuild property `$(IsWebConfigTransformDisabled)`:</span></span>

```dotnetcli
dotnet publish /p:IsWebConfigTransformDisabled=true
```

## <a name="additional-resources"></a><span data-ttu-id="eb1d2-142">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eb1d2-142">Additional resources</span></span>

* <span data-ttu-id="eb1d2-143">[Syntax der Web.config-Transformation für die Bereitstellung von Webanwendungsprojekten](/previous-versions/dd465326(v=vs.100))</span><span class="sxs-lookup"><span data-stu-id="eb1d2-143">[Web.config Transformation Syntax for Web Application Project Deployment](/previous-versions/dd465326(v=vs.100))</span></span>
* <span data-ttu-id="eb1d2-144">[Syntax der Web.config-Transformation für die Bereitstellung von Webanwendungsprojekten mit Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span><span class="sxs-lookup"><span data-stu-id="eb1d2-144">[Web.config Transformation Syntax for Web Project Deployment Using Visual Studio](/previous-versions/aspnet/dd465326(v=vs.110))</span></span>
