---
title: Konfiguration in ASP.NET Core
author: rick-anderson
description: In diesem Artikel erfahren Sie, wie Sie mit der Konfigurations-API eine ASP.NET Core-App konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 3/29/2020
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
uid: fundamentals/configuration/index
ms.openlocfilehash: ed40800f3e363cc3fd6f57492d76a42095e5b97b
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712557"
---
# <a name="configuration-in-aspnet-core"></a><span data-ttu-id="4ed55-103">Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4ed55-103">Configuration in ASP.NET Core</span></span>

<span data-ttu-id="4ed55-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="4ed55-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4ed55-105">Die Konfiguration in ASP.NET Core erfolgt mithilfe eines oder mehrerer [Konfigurationsanbieter](#cp).</span><span class="sxs-lookup"><span data-stu-id="4ed55-105">Configuration in ASP.NET Core is performed using one or more [configuration providers](#cp).</span></span> <span data-ttu-id="4ed55-106">Konfigurationsanbieter lesen Konfigurationsdaten aus Schlüssel-Wert-Paaren unter Verwendung verschiedener Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-106">Configuration providers read configuration data from key-value pairs using a variety of configuration sources:</span></span>

* <span data-ttu-id="4ed55-107">Einstellungsdateien, z. B. *appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="4ed55-107">Settings files, such as *appsettings.json*</span></span>
* <span data-ttu-id="4ed55-108">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="4ed55-108">Environment variables</span></span>
* <span data-ttu-id="4ed55-109">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4ed55-109">Azure Key Vault</span></span>
* <span data-ttu-id="4ed55-110">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4ed55-110">Azure App Configuration</span></span>
* <span data-ttu-id="4ed55-111">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="4ed55-111">Command-line arguments</span></span>
* <span data-ttu-id="4ed55-112">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="4ed55-112">Custom providers, installed or created</span></span>
* <span data-ttu-id="4ed55-113">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="4ed55-113">Directory files</span></span>
* <span data-ttu-id="4ed55-114">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="4ed55-114">In-memory .NET objects</span></span>

<span data-ttu-id="4ed55-115">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4ed55-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<a name="default"></a>

## <a name="default-configuration"></a><span data-ttu-id="4ed55-116">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="4ed55-116">Default configuration</span></span>

<span data-ttu-id="4ed55-117">ASP.NET Core-Web-Apps, die mit [dotnet new](/dotnet/core/tools/dotnet-new) oder Visual Studio erstellt wurden, generieren den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4ed55-117">ASP.NET Core web apps created with [dotnet new](/dotnet/core/tools/dotnet-new) or Visual Studio generate the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Program.cs?name=snippet&highlight=9)]

 <span data-ttu-id="4ed55-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="4ed55-118"><xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> provides default configuration for the app in the following order:</span></span>

1. <span data-ttu-id="4ed55-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource):  Fügt eine vorhandene `IConfiguration` als Quelle hinzu.</span><span class="sxs-lookup"><span data-stu-id="4ed55-119">[ChainedConfigurationProvider](xref:Microsoft.Extensions.Configuration.ChainedConfigurationSource) :  Adds an existing `IConfiguration` as a source.</span></span> <span data-ttu-id="4ed55-120">Im Fall einer Standardkonfiguration wird die [Host](#hvac)-Konfiguration hinzugefügt und als erste Quelle für die _App-_ Konfiguration festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-120">In the default configuration case, adds the [host](#hvac) configuration and setting it as the first source for the _app_ configuration.</span></span>
1. <span data-ttu-id="4ed55-121">[appsettings.json](#appsettingsjson) mithilfe des [JSON-Konfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4ed55-121">[appsettings.json](#appsettingsjson) using the [JSON configuration provider](#file-configuration-provider).</span></span>
1. <span data-ttu-id="4ed55-122">*appsettings.* `Environment` *.json* mithilfe des [JSON-Konfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4ed55-122">*appsettings.*`Environment`*.json* using the [JSON configuration provider](#file-configuration-provider).</span></span> <span data-ttu-id="4ed55-123">Beispielsweise *appsettings*.***Production***.*json* und *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-123">For example, *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json*.</span></span>
1. <span data-ttu-id="4ed55-124">[App-Geheimnisse](xref:security/app-secrets), wenn die App in der `Development`-Umgebung ausgeführt wird</span><span class="sxs-lookup"><span data-stu-id="4ed55-124">[App secrets](xref:security/app-secrets) when the app runs in the `Development` environment.</span></span>
1. <span data-ttu-id="4ed55-125">Umgebungsvariablen, die den [Umgebungsvariablen-Konfigurationsanbieter](#evcp) verwenden</span><span class="sxs-lookup"><span data-stu-id="4ed55-125">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="4ed55-126">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line) verwenden</span><span class="sxs-lookup"><span data-stu-id="4ed55-126">Command-line arguments using the [Command-line configuration provider](#command-line).</span></span>

<span data-ttu-id="4ed55-127">Später hinzugefügte Konfigurationsanbieter überschreiben vorherige Schlüsseleinstellungen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-127">Configuration providers that are added later override previous key settings.</span></span> <span data-ttu-id="4ed55-128">Wenn beispielsweise `MyKey` sowohl unter *appsettings.json* als auch unter Umgebung festgelegt wird, wird der Umgebungswert verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-128">For example, if `MyKey` is set in both *appsettings.json* and the environment, the environment value is used.</span></span> <span data-ttu-id="4ed55-129">Bei Verwendung der Standardkonfigurationsanbieter überschreibt der [Befehlszeilen-Konfigurationsanbieter](#clcp) alle anderen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="4ed55-129">Using the default configuration providers, the  [Command-line configuration provider](#clcp) overrides all other providers.</span></span>

<span data-ttu-id="4ed55-130">Weitere Informationen zu `CreateDefaultBuilder` finden Sie unter [Standardeinstellungen für den Generator](xref:fundamentals/host/generic-host#default-builder-settings).</span><span class="sxs-lookup"><span data-stu-id="4ed55-130">For more information on `CreateDefaultBuilder`, see [Default builder settings](xref:fundamentals/host/generic-host#default-builder-settings).</span></span>

<span data-ttu-id="4ed55-131">Im folgenden Code werden die aktivierten Konfigurationsanbieter in der Reihenfolge angezeigt, in der sie hinzugefügt wurden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-131">The following code displays the enabled configuration providers in the order they were added:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Index2.cshtml.cs?name=snippet)]

### <a name="appsettingsjson"></a><span data-ttu-id="4ed55-132">appsettings.json</span><span class="sxs-lookup"><span data-stu-id="4ed55-132">appsettings.json</span></span>

<span data-ttu-id="4ed55-133">Sehen Sie sich die nachfolgende Datei *appsettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-133">Consider the following *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="4ed55-134">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-134">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-135">Der Standard-<xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt die Konfiguration in der folgenden Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="4ed55-135">The default <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration in the following order:</span></span>

1. <span data-ttu-id="4ed55-136">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="4ed55-136">*appsettings.json*</span></span>
1. <span data-ttu-id="4ed55-137">*appsettings.* `Environment` *.json*: Beispielsweise die Dateien *appsettings*.***Production***.*json* und *appsettings*.***Development***.*json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-137">*appsettings.*`Environment`*.json* : For example, the *appsettings*.***Production***.*json* and *appsettings*.***Development***.*json* files.</span></span> <span data-ttu-id="4ed55-138">Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-138">The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span> <span data-ttu-id="4ed55-139">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-139">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="4ed55-140">*appsettings*.`Environment`.*json*-Werte überschreiben Schlüssel in *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-140">*appsettings*.`Environment`.*json* values override keys in *appsettings.json*.</span></span> <span data-ttu-id="4ed55-141">Standardmäßig sind dies z. B.:</span><span class="sxs-lookup"><span data-stu-id="4ed55-141">For example, by default:</span></span>

* <span data-ttu-id="4ed55-142">In der Entwicklung überschreibt die *appsettings*.***Development***.*json*-Konfiguration in *appsettings.json* gefundene Werte.</span><span class="sxs-lookup"><span data-stu-id="4ed55-142">In development, *appsettings*.***Development***.*json* configuration overwrites values found in *appsettings.json*.</span></span>
* <span data-ttu-id="4ed55-143">In der Produktion überschreibt die *appsettings*.***Production***.*json*-Konfiguration in *appsettings.json* gefundene Werte.</span><span class="sxs-lookup"><span data-stu-id="4ed55-143">In production, *appsettings*.***Production***.*json* configuration overwrites values found in *appsettings.json*.</span></span> <span data-ttu-id="4ed55-144">Dies ist beispielsweise bei der Bereitstellung der App in Azure der Fall.</span><span class="sxs-lookup"><span data-stu-id="4ed55-144">For example, when deploying the app to Azure.</span></span>

<a name="optpat"></a>

### <a name="bind-hierarchical-configuration-data-using-the-options-pattern"></a><span data-ttu-id="4ed55-145">Binden hierarchischer Konfigurationsdaten mit dem Optionsmuster</span><span class="sxs-lookup"><span data-stu-id="4ed55-145">Bind hierarchical configuration data using the options pattern</span></span>

[!INCLUDE[](~/includes/bind.md)]

<span data-ttu-id="4ed55-146">Wenn die [Standard](#default)-Konfiguration verwendet wird, werden die Dateien *appsettings.json* und *appsettings.* `Environment` *.json* mit [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75) aktiviert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-146">Using the [default](#default) configuration, the *appsettings.json* and *appsettings.*`Environment`*.json* files are enabled with [reloadOnChange: true](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L74-L75).</span></span> <span data-ttu-id="4ed55-147">Änderungen an den Dateien *appsettings.json* und *appsettings.* `Environment` *.json* ***nach*** dem Start der App werden vom [JSON-Konfigurationsanbieter](#jcp) gelesen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-147">Changes made to the *appsettings.json* and *appsettings.*`Environment`*.json* file ***after*** the app starts are read by the [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="4ed55-148">Weitere Informationen zum Hinzufügen zusätzlicher JSON-Konfigurationsdateien finden Sie unter [JSON-Konfigurationsanbieter](#jcp) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="4ed55-148">See [JSON configuration provider](#jcp) in this document for information on adding additional JSON configuration files.</span></span>

## <a name="combining-service-collection"></a><span data-ttu-id="4ed55-149">Kombinieren von Dienstsammlungen</span><span class="sxs-lookup"><span data-stu-id="4ed55-149">Combining service collection</span></span>

[!INCLUDE[](~/includes/combine-di.md)]

<a name="security"></a>

## <a name="security-and-secret-manager"></a><span data-ttu-id="4ed55-150">Sicherheits- und Secret-Manager</span><span class="sxs-lookup"><span data-stu-id="4ed55-150">Security and secret manager</span></span>

<span data-ttu-id="4ed55-151">Richtlinien für Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="4ed55-151">Configuration data guidelines:</span></span>

* <span data-ttu-id="4ed55-152">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="4ed55-152">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span> <span data-ttu-id="4ed55-153">Der [Secret Manager](xref:security/app-secrets) kann zum Speichern von Geheimnissen in der Entwicklungsumgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-153">The [Secret manager](xref:security/app-secrets) can be used to store secrets in development.</span></span>
* <span data-ttu-id="4ed55-154">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-154">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="4ed55-155">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="4ed55-155">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="4ed55-156">[Standardmäßig](#default) liest [Secret Manager](xref:security/app-secrets) Konfigurationseinstellungen nach dem Lesen von *appsettings.json* und *appsettings.* `Environment` *.json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-156">By [default](#default), [Secret manager](xref:security/app-secrets) reads configuration settings after *appsettings.json* and *appsettings.*`Environment`*.json*.</span></span>

<span data-ttu-id="4ed55-157">Weitere Informationen zum Speichern von Kennwörtern oder anderen vertraulichen Daten:</span><span class="sxs-lookup"><span data-stu-id="4ed55-157">For more information on storing passwords or other sensitive data:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="4ed55-158"><xref:security/app-secrets>:  Enthält Hinweise zur Verwendung von Umgebungsvariablen für das Speichern vertraulicher Daten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-158"><xref:security/app-secrets>:  Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="4ed55-159">Der Secret Manager verwendet den [Dateikonfigurationsanbieter](#fcp), um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="4ed55-159">The Secret Manager uses the [File configuration provider](#fcp) to store user secrets in a JSON file on the local system.</span></span>

<span data-ttu-id="4ed55-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="4ed55-160">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="4ed55-161">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-161">For more information, see <xref:security/key-vault-configuration>.</span></span>

<a name="evcp"></a>

## <a name="environment-variables"></a><span data-ttu-id="4ed55-162">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="4ed55-162">Environment variables</span></span>

<span data-ttu-id="4ed55-163">Bei Verwendung der [Standard](#default)-Konfiguration lädt der <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> die Konfiguration aus Schlüssel-Wert-Paaren der Umgebungsvariablen, nachdem *appsettings.json*, *appsettings.* `Environment` *.json* und [Secret Manager](xref:security/app-secrets) gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-163">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs after reading *appsettings.json*, *appsettings.*`Environment`*.json*, and [Secret manager](xref:security/app-secrets).</span></span> <span data-ttu-id="4ed55-164">Daher überschreiben aus der Umgebung gelesene Schlüsselwerte Werte, die aus *appsettings.json*, *appsettings.* `Environment` *.json* und Secret Manager gelesen wurden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-164">Therefore, key values read from the environment override values read from *appsettings.json*, *appsettings.*`Environment`*.json*, and Secret manager.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4ed55-165">Die folgenden `set`-Befehle:</span><span class="sxs-lookup"><span data-stu-id="4ed55-165">The following `set` commands:</span></span>

* <span data-ttu-id="4ed55-166">Legen die Umgebungsschlüssel und -werte des [vorangehenden Beispiels](#appsettingsjson) unter Windows fest.</span><span class="sxs-lookup"><span data-stu-id="4ed55-166">Set the environment keys and values of the [preceding example](#appsettingsjson) on Windows.</span></span>
* <span data-ttu-id="4ed55-167">Testen die Einstellungen bei Verwendung des [Beispieldownloads](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span><span class="sxs-lookup"><span data-stu-id="4ed55-167">Test the settings when using the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample).</span></span> <span data-ttu-id="4ed55-168">Der `dotnet run`-Befehl muss im Projektverzeichnis ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-168">The `dotnet run` command must be run in the project directory.</span></span>

```dotnetcli
set MyKey="My key from Environment"
set Position__Title=Environment_Editor
set Position__Name=Environment_Rick
dotnet run
```

<span data-ttu-id="4ed55-169">Die obigen Umgebungseinstellungen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-169">The preceding environment settings:</span></span>

* <span data-ttu-id="4ed55-170">Werden nur in Prozessen festgelegt, die über das Befehlsfenster gestartet werden, in dem sie festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-170">Are only set in processes launched from the command window they were set in.</span></span>
* <span data-ttu-id="4ed55-171">Werden nicht von Browsern gelesen, die mit Visual Studio gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-171">Won't be read by browsers launched with Visual Studio.</span></span>

<span data-ttu-id="4ed55-172">Die folgenden [setx](/windows-server/administration/windows-commands/setx)-Befehle können zum Festlegen der Umgebungsschlüssel und -werte unter Windows verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-172">The following [setx](/windows-server/administration/windows-commands/setx) commands can be used to set the environment keys and values on Windows.</span></span> <span data-ttu-id="4ed55-173">Anders als `set` werden `setx`-Einstellungen beibehalten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-173">Unlike `set`, `setx` settings are persisted.</span></span> <span data-ttu-id="4ed55-174">`/M` legt die Variable in der Systemumgebung fest.</span><span class="sxs-lookup"><span data-stu-id="4ed55-174">`/M` sets the variable in the system environment.</span></span> <span data-ttu-id="4ed55-175">Wenn der `/M`-Schalter nicht verwendet wird, wird eine Benutzerumgebungsvariable festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-175">If the `/M` switch isn't used, a user environment variable is set.</span></span>

```cmd
setx MyKey "My key from setx Environment" /M
setx Position__Title Setx_Environment_Editor /M
setx Position__Name Environment_Rick /M
```

<span data-ttu-id="4ed55-176">Testen Sie wie folgt, ob die obigen Befehle *appsettings.json* und *appsettings.* `Environment` *.json* überschreiben:</span><span class="sxs-lookup"><span data-stu-id="4ed55-176">To test that the preceding commands override *appsettings.json* and *appsettings.*`Environment`*.json*:</span></span>

* <span data-ttu-id="4ed55-177">Mit Visual Studio: Beenden Sie Visual Studio, und starten Sie dann Visual Studio neu.</span><span class="sxs-lookup"><span data-stu-id="4ed55-177">With Visual Studio: Exit and restart Visual Studio.</span></span>
* <span data-ttu-id="4ed55-178">Mit der Befehlszeilenschnittstelle: Starten Sie ein neues Befehlsfenster, und geben Sie `dotnet run` ein.</span><span class="sxs-lookup"><span data-stu-id="4ed55-178">With the CLI: Start a new command window and enter `dotnet run`.</span></span>

<span data-ttu-id="4ed55-179">Rufen Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> mit einer Zeichenfolge auf, um ein Präfix für Umgebungsvariablen anzugeben:</span><span class="sxs-lookup"><span data-stu-id="4ed55-179">Call <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> with a string to specify a prefix for environment variables:</span></span>

[!code-csharp[](~/fundamentals/configuration/index/samples/3.x/ConfigSample/Program.cs?name=snippet4&highlight=12)]

<span data-ttu-id="4ed55-180">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-180">In the preceding code:</span></span>

* <span data-ttu-id="4ed55-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` wird nach den [Standardkonfigurationsanbietern](#default) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-181">`config.AddEnvironmentVariables(prefix: "MyCustomPrefix_")` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="4ed55-182">Ein Beispiel für das Festlegen der Reihenfolge der Konfigurationsanbieter finden Sie unter [JSON-Konfigurationsanbieter](#jcp).</span><span class="sxs-lookup"><span data-stu-id="4ed55-182">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>
* <span data-ttu-id="4ed55-183">Umgebungsvariablen, die mit dem `MyCustomPrefix_`-Präfix festgelegt wurden, überschreiben die [Standardkonfigurationsanbieter](#default).</span><span class="sxs-lookup"><span data-stu-id="4ed55-183">Environment variables set with the `MyCustomPrefix_` prefix override the [default configuration providers](#default).</span></span> <span data-ttu-id="4ed55-184">Dies schließt Umgebungsvariablen ohne das Präfix ein.</span><span class="sxs-lookup"><span data-stu-id="4ed55-184">This includes environment variables without the prefix.</span></span>

<span data-ttu-id="4ed55-185">Das Präfix wird beim Lesen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-185">The prefix is stripped off when the configuration key-value pairs are read.</span></span>

<span data-ttu-id="4ed55-186">Mit den folgenden Befehlen wird das benutzerdefinierte Präfix getestet:</span><span class="sxs-lookup"><span data-stu-id="4ed55-186">The following commands test the custom prefix:</span></span>

```dotnetcli
set MyCustomPrefix_MyKey="My key with MyCustomPrefix_ Environment"
set MyCustomPrefix_Position__Title=Editor_with_customPrefix
set MyCustomPrefix_Position__Name=Environment_Rick_cp
dotnet run
```

<span data-ttu-id="4ed55-187">Die [Standardkonfiguration](#default) lädt Umgebungsvariablen und Befehlszeilenargumente, die das Präfix `DOTNET_` und `ASPNETCORE_` aufweisen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-187">The [default configuration](#default) loads environment variables and command line arguments prefixed with `DOTNET_` and `ASPNETCORE_`.</span></span> <span data-ttu-id="4ed55-188">Die Präfixe `DOTNET_` und `ASPNETCORE_` werden von ASP.NET Core für die [Host- und App-Konfiguration](xref:fundamentals/host/generic-host#host-configuration), jedoch nicht für die Benutzerkonfiguration verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-188">The `DOTNET_` and `ASPNETCORE_` prefixes are used by ASP.NET Core for [host and app configuration](xref:fundamentals/host/generic-host#host-configuration), but not for user configuration.</span></span> <span data-ttu-id="4ed55-189">Weitere Informationen zur Host- und App-Konfiguration finden Sie unter [.NET Generic Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="4ed55-189">For more information on host and app configuration, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

<span data-ttu-id="4ed55-190">Wählen Sie in [Azure App Service](https://azure.microsoft.com/services/app-service/) auf der Seite **Einstellungen > Konfiguration** die Option **Neue Anwendungseinstellung** aus.</span><span class="sxs-lookup"><span data-stu-id="4ed55-190">On [Azure App Service](https://azure.microsoft.com/services/app-service/), select **New application setting** on the **Settings > Configuration** page.</span></span> <span data-ttu-id="4ed55-191">Anwendungseinstellungen von Azure App Service werden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-191">Azure App Service application settings are:</span></span>

* <span data-ttu-id="4ed55-192">Im Ruhezustand verschlüsselt und über einen verschlüsselten Kanal übermittelt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-192">Encrypted at rest and transmitted over an encrypted channel.</span></span>
* <span data-ttu-id="4ed55-193">Als Umgebungsvariablen verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="4ed55-193">Exposed as environment variables.</span></span>

<span data-ttu-id="4ed55-194">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="4ed55-194">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="4ed55-195">Informationen zu Azure-Datenbankverbindungszeichenfolgen finden Sie unter [Präfixe für Verbindungszeichenfolgen](#constr).</span><span class="sxs-lookup"><span data-stu-id="4ed55-195">See [Connection string prefixes](#constr) for information on Azure database connection strings.</span></span>

### <a name="environment-variables-set-in-launchsettingsjson"></a><span data-ttu-id="4ed55-196">In „launchSettings.json“ festgelegte Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="4ed55-196">Environment variables set in launchSettings.json</span></span>

<span data-ttu-id="4ed55-197">Umgebungsvariablen, die in der Datei *launchSettings.json* festgelegt sind, überschreiben diejenigen, die in der Systemumgebung festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="4ed55-197">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<a name="clcp"></a>

## <a name="command-line"></a><span data-ttu-id="4ed55-198">Befehlszeile</span><span class="sxs-lookup"><span data-stu-id="4ed55-198">Command-line</span></span>

<span data-ttu-id="4ed55-199">Bei Verwendung der [Standard](#default)-Konfiguration lädt der <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments nach den folgenden Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-199">Using the [default](#default) configuration, the <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs after the following configuration sources:</span></span>

* <span data-ttu-id="4ed55-200">Dateien *appsettings.json* und *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-200">*appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="4ed55-201">[App-Geheimnisse (Secret Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="4ed55-201">[App secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4ed55-202">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-202">Environment variables.</span></span>

<span data-ttu-id="4ed55-203">[Standardmäßig](#default) überschreiben in der Befehlszeile festgelegte Konfigurationswerte die Konfigurationswerte, die mit allen anderen Konfigurationsanbietern festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-203">By [default](#default), configuration values set on the command-line override configuration values set with all the other configuration providers.</span></span>

### <a name="command-line-arguments"></a><span data-ttu-id="4ed55-204">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="4ed55-204">Command-line arguments</span></span>

<span data-ttu-id="4ed55-205">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `=` fest:</span><span class="sxs-lookup"><span data-stu-id="4ed55-205">The following command sets keys and values using `=`:</span></span>

```dotnetcli
dotnet run MyKey="My key from command line" Position:Title=Cmd Position:Name=Cmd_Rick
```

<span data-ttu-id="4ed55-206">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `/` fest:</span><span class="sxs-lookup"><span data-stu-id="4ed55-206">The following command sets keys and values using `/`:</span></span>

```dotnetcli
dotnet run /MyKey "Using /" /Position:Title=Cmd_ /Position:Name=Cmd_Rick
```

<span data-ttu-id="4ed55-207">Der folgende Befehl legt Schlüssel und Werte unter Verwendung von `--` fest:</span><span class="sxs-lookup"><span data-stu-id="4ed55-207">The following command sets keys and values using `--`:</span></span>

```dotnetcli
dotnet run --MyKey "Using --" --Position:Title=Cmd-- --Position:Name=Cmd--Rick
```

<span data-ttu-id="4ed55-208">Der Schlüsselwert:</span><span class="sxs-lookup"><span data-stu-id="4ed55-208">The key value:</span></span>

* <span data-ttu-id="4ed55-209">Muss auf `=` folgen, oder der Schlüssel muss das Präfix `--` oder `/` aufweisen, wenn der Wert auf ein Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-209">Must follow `=`, or the key must have a prefix of `--` or `/` when the value follows a space.</span></span>
* <span data-ttu-id="4ed55-210">Ist nicht erforderlich, wenn `=` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4ed55-210">Isn't required if `=` is used.</span></span> <span data-ttu-id="4ed55-211">Beispielsweise `MySetting=`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-211">For example, `MySetting=`.</span></span>

<span data-ttu-id="4ed55-212">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare des Befehlszeilenarguments, die `=` verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-212">Within the same command, don't mix command-line argument key-value pairs that use `=` with key-value pairs that use a space.</span></span>

### <a name="switch-mappings"></a><span data-ttu-id="4ed55-213">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="4ed55-213">Switch mappings</span></span>

<span data-ttu-id="4ed55-214">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von **Schlüsselnamen**.</span><span class="sxs-lookup"><span data-stu-id="4ed55-214">Switch mappings allow **key** name replacement logic.</span></span> <span data-ttu-id="4ed55-215">Stellen Sie ein Wörterbuch mit Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereit.</span><span class="sxs-lookup"><span data-stu-id="4ed55-215">Provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="4ed55-216">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="4ed55-216">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="4ed55-217">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-217">If the command-line key is found in the dictionary, the dictionary value is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="4ed55-218">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="4ed55-218">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="4ed55-219">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="4ed55-219">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="4ed55-220">Switches müssen mit `-` oder `--` beginnen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-220">Switches must start with `-` or `--`.</span></span>
* <span data-ttu-id="4ed55-221">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-221">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="4ed55-222">Wenn Sie ein Switchmappingwörterbuch verwenden möchten, übergeben Sie es an den `AddCommandLine`-Abruf:</span><span class="sxs-lookup"><span data-stu-id="4ed55-222">To use a switch mappings dictionary, pass it into the call to `AddCommandLine`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramSwitch.cs?name=snippet&highlight=10-18,23)]

<span data-ttu-id="4ed55-223">Der folgende Code zeigt die Schlüsselwerte für die ersetzten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="4ed55-223">The following code shows the key values for the replaced keys:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test3.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-224">Mit dem folgenden Befehl kann die Schlüsselersetzung getestet werden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-224">The following command works to test key replacement:</span></span>

```dotnetcli
dotnet run -k1 value1 -k2 value2 --alt3=value2 /alt4=value3 --alt5 value5 /alt6 value6
```

<span data-ttu-id="4ed55-225">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-225">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="4ed55-226">Der `AddCommandLine`-Aufruf der `CreateDefaultBuilder`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-226">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch-mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4ed55-227">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der `AddCommandLine`-Methode der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-227">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch-mapping dictionary.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="4ed55-228">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="4ed55-228">Hierarchical configuration data</span></span>

<span data-ttu-id="4ed55-229">Die Konfigurations-API liest hierarchische Konfigurationsdaten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="4ed55-229">The Configuration API reads hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="4ed55-230">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-230">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *appsettings.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/appsettings.json)]

<span data-ttu-id="4ed55-231">Der folgende Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zeigt einige der Konfigurationseinstellungen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-231">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-232">Die bevorzugte Methode zum Lesen hierarchischer Konfigurationsdaten ist die Verwendung des Optionsmusters.</span><span class="sxs-lookup"><span data-stu-id="4ed55-232">The preferred way to read hierarchical configuration data is using the options pattern.</span></span> <span data-ttu-id="4ed55-233">Weitere Informationen finden Sie unter [Binden hierarchischer Konfigurationsdaten](#optpat) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="4ed55-233">For more information, see [Bind hierarchical configuration data](#optpat) in this document.</span></span>

<span data-ttu-id="4ed55-234">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-234"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="4ed55-235">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-235">These methods are described later in [GetSection, GetChildren, and Exists](#getsection).</span></span>

<!--
[Azure Key Vault configuration provider](xref:security/key-vault-configuration) implement change detection.
-->

## <a name="configuration-keys-and-values"></a><span data-ttu-id="4ed55-236">Konfigurationsschlüssel und -werte</span><span class="sxs-lookup"><span data-stu-id="4ed55-236">Configuration keys and values</span></span>

<span data-ttu-id="4ed55-237">Konfigurationsschlüssel:</span><span class="sxs-lookup"><span data-stu-id="4ed55-237">Configuration keys:</span></span>

* <span data-ttu-id="4ed55-238">Groß-/Kleinschreibung wird nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-238">Are case-insensitive.</span></span> <span data-ttu-id="4ed55-239">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="4ed55-239">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="4ed55-240">Wenn ein Schlüssel und ein Wert in mehreren Konfigurationsanbietern festgelegt ist, wird der Wert des zuletzt hinzugefügten Anbieters verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-240">If a key and value is set in more than one configuration providers, the value from the last provider added is used.</span></span> <span data-ttu-id="4ed55-241">Weitere Informationen finden Sie unter [Standardkonfiguration](#default).</span><span class="sxs-lookup"><span data-stu-id="4ed55-241">For more information, see [Default configuration](#default).</span></span>
* <span data-ttu-id="4ed55-242">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="4ed55-242">Hierarchical keys</span></span>
  * <span data-ttu-id="4ed55-243">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-243">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="4ed55-244">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-244">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="4ed55-245">Ein doppelter Unterstrich (`__`) wird von allen Plattformen unterstützt und automatisch in einen Doppelpunkt (`:`) umgewandelt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-245">A double underscore, `__`, is supported by all platforms and is automatically converted into a colon `:`.</span></span>
  * <span data-ttu-id="4ed55-246">In Azure Key Vault verwenden hierarchische Schlüssel `--` als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-246">In Azure Key Vault, hierarchical keys use `--` as a separator.</span></span> <span data-ttu-id="4ed55-247">Der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) ersetzt `--` automatisch durch `:`, wenn die Geheimnisse in die Konfiguration der App geladen werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-247">The [Azure Key Vault configuration provider](xref:security/key-vault-configuration) automatically replaces `--` with a `:` when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="4ed55-248"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="4ed55-248">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4ed55-249">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#boa) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-249">Array binding is described in the [Bind an array to a class](#boa) section.</span></span>

<span data-ttu-id="4ed55-250">Konfigurationswerte:</span><span class="sxs-lookup"><span data-stu-id="4ed55-250">Configuration values:</span></span>

* <span data-ttu-id="4ed55-251">Sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-251">Are strings.</span></span>
* <span data-ttu-id="4ed55-252">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-252">Null values can't be stored in configuration or bound to objects.</span></span>

<a name="cp"></a>

## <a name="configuration-providers"></a><span data-ttu-id="4ed55-253">Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-253">Configuration providers</span></span>

<span data-ttu-id="4ed55-254">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="4ed55-254">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="4ed55-255">Anbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-255">Provider</span></span> | <span data-ttu-id="4ed55-256">Bereitstellung der Konfiguration über</span><span class="sxs-lookup"><span data-stu-id="4ed55-256">Provides configuration from</span></span> |
| -------- | ----------------------------------- |
| [<span data-ttu-id="4ed55-257">Azure Key Vault-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-257">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration) | <span data-ttu-id="4ed55-258">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4ed55-258">Azure Key Vault</span></span> |
| [<span data-ttu-id="4ed55-259">Azure-App-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-259">Azure App configuration provider</span></span>](/azure/azure-app-configuration/quickstart-aspnet-core-app) | <span data-ttu-id="4ed55-260">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4ed55-260">Azure App Configuration</span></span> |
| [<span data-ttu-id="4ed55-261">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-261">Command-line configuration provider</span></span>](#clcp) | <span data-ttu-id="4ed55-262">Befehlszeilenparameter</span><span class="sxs-lookup"><span data-stu-id="4ed55-262">Command-line parameters</span></span> |
| [<span data-ttu-id="4ed55-263">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-263">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="4ed55-264">Benutzerdefinierte Quelle</span><span class="sxs-lookup"><span data-stu-id="4ed55-264">Custom source</span></span> |
| [<span data-ttu-id="4ed55-265">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-265">Environment Variables configuration provider</span></span>](#evcp) | <span data-ttu-id="4ed55-266">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="4ed55-266">Environment variables</span></span> |
| [<span data-ttu-id="4ed55-267">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-267">File configuration provider</span></span>](#file-configuration-provider) | <span data-ttu-id="4ed55-268">INI-, JSON- und XML-Dateien</span><span class="sxs-lookup"><span data-stu-id="4ed55-268">INI, JSON, and XML files</span></span> |
| [<span data-ttu-id="4ed55-269">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-269">Key-per-file configuration provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="4ed55-270">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="4ed55-270">Directory files</span></span> |
| [<span data-ttu-id="4ed55-271">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-271">Memory configuration provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="4ed55-272">In-Memory-Sammlungen</span><span class="sxs-lookup"><span data-stu-id="4ed55-272">In-memory collections</span></span> |
| [<span data-ttu-id="4ed55-273">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="4ed55-273">Secret Manager</span></span>](xref:security/app-secrets)  | <span data-ttu-id="4ed55-274">Datei im Benutzerprofilverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4ed55-274">File in the user profile directory</span></span> |

<span data-ttu-id="4ed55-275">Konfigurationsquellen werden in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="4ed55-275">Configuration sources are read in the order that their configuration providers are specified.</span></span> <span data-ttu-id="4ed55-276">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="4ed55-276">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="4ed55-277">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="4ed55-277">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="4ed55-278">*appsettings.json*</span><span class="sxs-lookup"><span data-stu-id="4ed55-278">*appsettings.json*</span></span>
1. <span data-ttu-id="4ed55-279">*appsettings*.`Environment`.*json*</span><span class="sxs-lookup"><span data-stu-id="4ed55-279">*appsettings*.`Environment`.*json*</span></span>
1. [<span data-ttu-id="4ed55-280">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="4ed55-280">Secret Manager</span></span>](xref:security/app-secrets)
1. <span data-ttu-id="4ed55-281">Umgebungsvariablen, die den [Umgebungsvariablen-Konfigurationsanbieter](#evcp) verwenden</span><span class="sxs-lookup"><span data-stu-id="4ed55-281">Environment variables using the [Environment Variables configuration provider](#evcp).</span></span>
1. <span data-ttu-id="4ed55-282">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) verwenden</span><span class="sxs-lookup"><span data-stu-id="4ed55-282">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>

<span data-ttu-id="4ed55-283">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle hinzugefügt, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="4ed55-283">A common practice is to add the Command-line configuration provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="4ed55-284">Die obige Sequenz von Anbietern wird in der [Standardkonfiguration](#default) verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-284">The preceding sequence of providers is used in the [default configuration](#default).</span></span>

<a name="constr"></a>

### <a name="connection-string-prefixes"></a><span data-ttu-id="4ed55-285">Präfixe für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="4ed55-285">Connection string prefixes</span></span>

<span data-ttu-id="4ed55-286">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-286">The Configuration API has special processing rules for four connection string environment variables.</span></span> <span data-ttu-id="4ed55-287">Diese Verbindungszeichenfolgen sind beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung beteiligt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-287">These connection strings are involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="4ed55-288">Umgebungsvariablen mit den in der Tabelle aufgeführten Präfixen werden mit der [Standardkonfiguration](#default) in die App geladen bzw. wenn kein Präfix für `AddEnvironmentVariables` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="4ed55-288">Environment variables with the prefixes shown in the table are loaded into the app with the [default configuration](#default) or when no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="4ed55-289">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="4ed55-289">Connection string prefix</span></span> | <span data-ttu-id="4ed55-290">Anbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-290">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="4ed55-291">Benutzerdefinierter Anbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-291">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="4ed55-292">MySQL</span><span class="sxs-lookup"><span data-stu-id="4ed55-292">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="4ed55-293">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="4ed55-293">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="4ed55-294">SQL Server</span><span class="sxs-lookup"><span data-stu-id="4ed55-294">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="4ed55-295">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="4ed55-295">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="4ed55-296">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-296">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="4ed55-297">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="4ed55-297">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="4ed55-298">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="4ed55-298">Environment variable key</span></span> | <span data-ttu-id="4ed55-299">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="4ed55-299">Converted configuration key</span></span> | <span data-ttu-id="4ed55-300">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="4ed55-300">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4ed55-301">Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-301">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4ed55-302">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-302">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4ed55-303">Wert: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="4ed55-303">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4ed55-304">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-304">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4ed55-305">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4ed55-305">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4ed55-306">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-306">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4ed55-307">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4ed55-307">Value: `System.Data.SqlClient`</span></span>  |

<a name="fcp"></a>

## <a name="file-configuration-provider"></a><span data-ttu-id="4ed55-308">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-308">File configuration provider</span></span>

<span data-ttu-id="4ed55-309"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="4ed55-309"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="4ed55-310">Die folgenden Konfigurationsanbieter leiten sich vom `FileConfigurationProvider` ab:</span><span class="sxs-lookup"><span data-stu-id="4ed55-310">The following configuration providers derive from `FileConfigurationProvider`:</span></span>

* [<span data-ttu-id="4ed55-311">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-311">INI configuration provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="4ed55-312">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-312">JSON configuration provider</span></span>](#jcp)
* [<span data-ttu-id="4ed55-313">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-313">XML configuration provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="4ed55-314">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-314">INI configuration provider</span></span>

<span data-ttu-id="4ed55-315"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="4ed55-315">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="4ed55-316">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-316">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramINI.cs?name=snippet&highlight=10-30)]

<span data-ttu-id="4ed55-317">Im obigen Code werden Einstellungen in den Dateien *MyIniConfig.ini* und *MyIniConfig*.`Environment`.*ini* überschrieben durch Einstellungen im:</span><span class="sxs-lookup"><span data-stu-id="4ed55-317">In the preceding code, settings in the *MyIniConfig.ini* and  *MyIniConfig*.`Environment`.*ini* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="4ed55-318">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-318">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="4ed55-319">[Befehlszeilen-Konfigurationsanbieter](#clcp)</span><span class="sxs-lookup"><span data-stu-id="4ed55-319">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4ed55-320">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyIniConfig.ini*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-320">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyIniConfig.ini* file:</span></span>

[!code-ini[](index/samples/3.x/ConfigSample/MyIniConfig.ini)]

<span data-ttu-id="4ed55-321">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-321">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<a name="jcp"></a>

### <a name="json-configuration-provider"></a><span data-ttu-id="4ed55-322">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-322">JSON configuration provider</span></span>

<span data-ttu-id="4ed55-323">Der <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="4ed55-323">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs.</span></span>

<span data-ttu-id="4ed55-324">Überladungen können Folgendes angeben:</span><span class="sxs-lookup"><span data-stu-id="4ed55-324">Overloads can specify:</span></span>

* <span data-ttu-id="4ed55-325">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="4ed55-325">Whether the file is optional.</span></span>
* <span data-ttu-id="4ed55-326">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="4ed55-326">Whether the configuration is reloaded if the file changes.</span></span>

<span data-ttu-id="4ed55-327">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4ed55-327">Consider the following code:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON.cs?name=snippet&highlight=12-14)]

<span data-ttu-id="4ed55-328">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="4ed55-328">The preceding code:</span></span>

* <span data-ttu-id="4ed55-329">Konfiguriert den JSON-Konfigurationsanbieter, sodass dieser die Datei *MyConfig.json* mit den folgenden Optionen lädt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-329">Configures the JSON configuration provider to load the *MyConfig.json* file with the following options:</span></span>
  * <span data-ttu-id="4ed55-330">`optional: true`: Die Datei ist optional.</span><span class="sxs-lookup"><span data-stu-id="4ed55-330">`optional: true`: The file is optional.</span></span>
  * <span data-ttu-id="4ed55-331">`reloadOnChange: true` : Die Datei wird erneut geladen, wenn Änderungen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-331">`reloadOnChange: true` : The file is reloaded when changes are saved.</span></span>
* <span data-ttu-id="4ed55-332">Liest die [Standardkonfigurationsanbieter](#default) vor der Datei *MyConfig.json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-332">Reads the [default configuration providers](#default) before the *MyConfig.json* file.</span></span> <span data-ttu-id="4ed55-333">Einstellungen in der Datei *MyConfig.json* überschreiben die Einstellung in den Standardkonfigurationsanbietern, einschließlich des [Umgebungsvariablen-Konfigurationsanbieters](#evcp) und des [Befehlszeilen-Konfigurationsanbieters](#clcp).</span><span class="sxs-lookup"><span data-stu-id="4ed55-333">Settings in the *MyConfig.json* file override setting in the default configuration providers, including the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4ed55-334">In der Regel möchten Sie ***nicht***, dass eine benutzerdefinierte JSON-Datei Werte überschreibt, die im [Umgebungsvariablen-Konfigurationsanbieter](#evcp) und im [Befehlszeilen-Konfigurationsanbieter](#clcp) festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="4ed55-334">You typically ***don't*** want a custom JSON file overriding values set in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4ed55-335">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-335">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSON2.cs?name=snippet)]

<span data-ttu-id="4ed55-336">Im obigen Code gilt für Einstellungen in den Dateien *MyConfig.json* und *MyConfig*.`Environment`.*json*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-336">In the preceding code, settings in the *MyConfig.json* and  *MyConfig*.`Environment`.*json* files:</span></span>

* <span data-ttu-id="4ed55-337">Sie überschreiben Einstellungen in den Dateien *appsettings.json* und *appsettings*.`Environment`.*json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-337">Override settings in the *appsettings.json* and *appsettings*.`Environment`.*json* files.</span></span>
* <span data-ttu-id="4ed55-338">Sie werden durch Einstellungen im [Umgebungsvariablen-Konfigurationsanbieter](#evcp) und im [Befehlszeilen-Konfigurationsanbieter](#clcp) überschrieben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-338">Are overridden by settings in the [Environment variables configuration provider](#evcp) and the [Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4ed55-339">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyConfig.json*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-339">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following  *MyConfig.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyConfig.json)]

<span data-ttu-id="4ed55-340">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-340">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

### <a name="xml-configuration-provider"></a><span data-ttu-id="4ed55-341">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-341">XML configuration provider</span></span>

<span data-ttu-id="4ed55-342"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="4ed55-342">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="4ed55-343">Mit dem folgenden Code werden alle Konfigurationsanbieter gelöscht und mehrere Konfigurationsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-343">The following code clears all the configuration providers and adds several configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramXML.cs?name=snippet)]

<span data-ttu-id="4ed55-344">Im obigen Code werden Einstellungen in den Dateien *MyXMLFile.xml* und *MyXMLFile*.`Environment`.*xml* überschrieben durch Einstellungen im:</span><span class="sxs-lookup"><span data-stu-id="4ed55-344">In the preceding code, settings in the *MyXMLFile.xml* and  *MyXMLFile*.`Environment`.*xml* files are overridden by settings in the:</span></span>

* [<span data-ttu-id="4ed55-345">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-345">Environment variables configuration provider</span></span>](#evcp)
* <span data-ttu-id="4ed55-346">[Befehlszeilen-Konfigurationsanbieter](#clcp)</span><span class="sxs-lookup"><span data-stu-id="4ed55-346">[Command-line configuration provider](#clcp).</span></span>

<span data-ttu-id="4ed55-347">Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) enthält die folgende Datei *MyXMLFile.xml*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-347">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) contains the following *MyXMLFile.xml* file:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile.xml)]

<span data-ttu-id="4ed55-348">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) sind mehrere der vorherigen Konfigurationseinstellungen zu sehen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-348">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays several of the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-349">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="4ed55-349">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

[!code-xml[](index/samples/3.x/ConfigSample/MyXMLFile3.xml)]

<span data-ttu-id="4ed55-350">Der folgende Code liest die vorherige Konfigurationsdatei und zeigt die Schlüssel und Werte an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-350">The following code reads the previous configuration file and displays the keys and values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/XML/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-351">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-351">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="4ed55-352">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-352">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4ed55-353">key:attribute</span><span class="sxs-lookup"><span data-stu-id="4ed55-353">key:attribute</span></span>
* <span data-ttu-id="4ed55-354">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="4ed55-354">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="4ed55-355">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-355">Key-per-file configuration provider</span></span>

<span data-ttu-id="4ed55-356"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="4ed55-356">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="4ed55-357">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="4ed55-357">The key is the file name.</span></span> <span data-ttu-id="4ed55-358">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="4ed55-358">The value contains the file's contents.</span></span> <span data-ttu-id="4ed55-359">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarios verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-359">The Key-per-file configuration provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="4ed55-360">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="4ed55-360">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="4ed55-361">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="4ed55-361">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="4ed55-362">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-362">Overloads permit specifying:</span></span>

* <span data-ttu-id="4ed55-363">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="4ed55-363">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="4ed55-364">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="4ed55-364">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="4ed55-365">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-365">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="4ed55-366">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-366">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="4ed55-367">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="4ed55-367">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

<a name="mcp"></a>

## <a name="memory-configuration-provider"></a><span data-ttu-id="4ed55-368">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-368">Memory configuration provider</span></span>

<span data-ttu-id="4ed55-369"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="4ed55-369">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="4ed55-370">Der folgende Code fügt eine Arbeitsspeichersammlung zum Konfigurationssystem hinzu:</span><span class="sxs-lookup"><span data-stu-id="4ed55-370">The following code adds a memory collection to the configuration system:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet6)]

<span data-ttu-id="4ed55-371">Der folgende Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) zeigt die vorherigen Konfigurationseinstellungen an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-371">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample) displays the preceding configurations settings:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Test.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-372">Im obigen Code wird `config.AddInMemoryCollection(Dict)` nach den [Standardkonfigurationsanbietern](#default) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-372">In the preceding code, `config.AddInMemoryCollection(Dict)` is added after the [default configuration providers](#default).</span></span> <span data-ttu-id="4ed55-373">Ein Beispiel für das Festlegen der Reihenfolge der Konfigurationsanbieter finden Sie unter [JSON-Konfigurationsanbieter](#jcp).</span><span class="sxs-lookup"><span data-stu-id="4ed55-373">For an example of ordering the configuration providers, see [JSON configuration provider](#jcp).</span></span>

<span data-ttu-id="4ed55-374">Ein weiteres Beispiel für die Verwendung von `MemoryConfigurationProvider` finden Sie unter [Binden eines Arrays](#boa).</span><span class="sxs-lookup"><span data-stu-id="4ed55-374">See [Bind an array](#boa) for another example using `MemoryConfigurationProvider`.</span></span>

## <a name="getvalue"></a><span data-ttu-id="4ed55-375">GetValue</span><span class="sxs-lookup"><span data-stu-id="4ed55-375">GetValue</span></span>

<span data-ttu-id="4ed55-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Einzelwert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ:</span><span class="sxs-lookup"><span data-stu-id="4ed55-376">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified type:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestNum.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-377">Wenn im obigen Code `NumberKey` nicht in der Konfiguration gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-377">In the preceding code,  if `NumberKey` isn't found in the configuration, the default value of `99` is used.</span></span>

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="4ed55-378">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="4ed55-378">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="4ed55-379">Beachten Sie für die nachstehenden Beispiele die folgende Datei *MySubsection.json*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-379">For the examples that follow, consider the following *MySubsection.json* file:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MySubsection.json)]

<span data-ttu-id="4ed55-380">Der folgende Code fügt *MySubsection.json* zu den Konfigurationsanbietern hinzu:</span><span class="sxs-lookup"><span data-stu-id="4ed55-380">The following code adds *MySubsection.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONsection.cs?name=snippet)]

### <a name="getsection"></a><span data-ttu-id="4ed55-381">GetSection</span><span class="sxs-lookup"><span data-stu-id="4ed55-381">GetSection</span></span>

<span data-ttu-id="4ed55-382">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) gibt einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel zurück.</span><span class="sxs-lookup"><span data-stu-id="4ed55-382">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) returns a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="4ed55-383">Der folgende Code gibt Werte für `section1` zurück:</span><span class="sxs-lookup"><span data-stu-id="4ed55-383">The following code returns values for `section1`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-384">Der folgende Code gibt Werte für `section2:subsection0` zurück:</span><span class="sxs-lookup"><span data-stu-id="4ed55-384">The following code returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection2.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-385">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="4ed55-385">`GetSection` never returns `null`.</span></span> <span data-ttu-id="4ed55-386">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-386">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="4ed55-387">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-387">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="4ed55-388">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4ed55-388">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren-and-exists"></a><span data-ttu-id="4ed55-389">GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="4ed55-389">GetChildren and Exists</span></span>

<span data-ttu-id="4ed55-390">Mit dem folgenden Code wird [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) abgerufen, und es werden Werte für `section2:subsection0` zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="4ed55-390">The following code calls [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) and returns values for `section2:subsection0`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/TestSection4.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-391">Der obige Code ruft [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) auf, um zu überprüfen, ob der Abschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="4ed55-391">The preceding code calls [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to verify the  section exists:</span></span>

 <a name="boa"></a>

## <a name="bind-an-array"></a><span data-ttu-id="4ed55-392">Binden eines Arrays</span><span class="sxs-lookup"><span data-stu-id="4ed55-392">Bind an array</span></span>

<span data-ttu-id="4ed55-393">[ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) unterstützt das Binden von Arrays an Objekte mithilfe von Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="4ed55-393">The [ConfigurationBinder.Bind](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*) supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4ed55-394">Jedes Arrayformat, das ein numerisches Schlüsselsegment verfügbar macht, kann ein Array an ein [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object)-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-394">Any array format that exposes a numeric key segment is capable of array binding to a [POCO](https://wikipedia.org/wiki/Plain_Old_CLR_Object) class array.</span></span>

<span data-ttu-id="4ed55-395">Beachten Sie *MyArray.json* aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span><span class="sxs-lookup"><span data-stu-id="4ed55-395">Consider *MyArray.json* from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples/3.x/ConfigSample):</span></span>

[!code-json[](index/samples/3.x/ConfigSample/MyArray.json)]

<span data-ttu-id="4ed55-396">Der folgende Code fügt *MyArray.json* zu den Konfigurationsanbietern hinzu:</span><span class="sxs-lookup"><span data-stu-id="4ed55-396">The following code adds *MyArray.json* to the configuration providers:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramJSONarray.cs?name=snippet)]

<span data-ttu-id="4ed55-397">Der folgende Code liest die Konfiguration und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-397">The following code reads the configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-398">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="4ed55-398">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value00
Index: 1  Value: value10
Index: 2  Value: value20
Index: 3  Value: value40
Index: 4  Value: value50
```

<span data-ttu-id="4ed55-399">In der obigen Ausgabe hat Index 3 den Wert `value40`, der `"4": "value40",` in *MyArray.json* entspricht.</span><span class="sxs-lookup"><span data-stu-id="4ed55-399">In the preceding output, Index 3 has value `value40`, corresponding to `"4": "value40",` in *MyArray.json*.</span></span> <span data-ttu-id="4ed55-400">Die gebundenen Arrayindizes sind kontinuierlich und nicht an den Konfigurationsschlüsselindex gebunden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-400">The bound array indices are continuous and not bound to the configuration key index.</span></span> <span data-ttu-id="4ed55-401">Der Konfigurationsbinder ist nicht in der Lage, NULL-Werte zu binden oder NULL-Einträge in gebundenen Objekten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-401">The configuration binder isn't capable of binding null values or creating null entries in bound objects</span></span>

<span data-ttu-id="4ed55-402">Mit dem folgenden Code wird die `array:entries`-Konfiguration mit der <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode geladen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-402">The  following code loads the `array:entries` configuration with the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet)]

<span data-ttu-id="4ed55-403">Der folgende Code liest die Konfiguration im `arrayDict` `Dictionary` und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-403">The following code reads the configuration in the `arrayDict` `Dictionary` and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-404">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="4ed55-404">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value4
Index: 4  Value: value5
```

<span data-ttu-id="4ed55-405">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-405">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="4ed55-406">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-406">When configuration data containing an array is bound, the array indices in the configuration keys are used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="4ed55-407">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-407">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="4ed55-408">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`-Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das Schlüssel-Wert-Paar von Index &num;3 liest.</span><span class="sxs-lookup"><span data-stu-id="4ed55-408">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that reads the index &num;3 key/value pair.</span></span> <span data-ttu-id="4ed55-409">Beachten Sie die folgende Datei *Value3.json* aus dem Beispieldownload:</span><span class="sxs-lookup"><span data-stu-id="4ed55-409">Consider the following *Value3.json* file from the sample download:</span></span>

[!code-json[](index/samples/3.x/ConfigSample/Value3.json)]

<span data-ttu-id="4ed55-410">Der folgende Code enthält die Konfiguration für *Value3.json* und das `arrayDict` `Dictionary`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-410">The following code includes configuration for *Value3.json* and the `arrayDict` `Dictionary`:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/ProgramArray.cs?name=snippet2)]

<span data-ttu-id="4ed55-411">Der folgende Code liest die obige Konfiguration und zeigt die Werte an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-411">The following code reads the preceding configuration and displays the values:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/Pages/Array.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-412">Der obige Code erzeugt die folgende Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="4ed55-412">The preceding code returns the following output:</span></span>

```text
Index: 0  Value: value0
Index: 1  Value: value1
Index: 2  Value: value2
Index: 3  Value: value3
Index: 4  Value: value4
Index: 5  Value: value5
```

<span data-ttu-id="4ed55-413">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="4ed55-413">Custom configuration providers aren't required to implement array binding.</span></span>

## <a name="custom-configuration-provider"></a><span data-ttu-id="4ed55-414">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-414">Custom configuration provider</span></span>

<span data-ttu-id="4ed55-415">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="4ed55-415">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="4ed55-416">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="4ed55-416">The provider has the following characteristics:</span></span>

* <span data-ttu-id="4ed55-417">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-417">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="4ed55-418">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-418">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="4ed55-419">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="4ed55-419">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="4ed55-420">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="4ed55-420">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="4ed55-421">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="4ed55-421">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="4ed55-422">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4ed55-422">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="4ed55-423">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-423">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="4ed55-424">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-424">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="4ed55-425">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-425">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="4ed55-426">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-426">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="4ed55-427">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-427">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="4ed55-428">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-428">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="4ed55-429">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="4ed55-429">The configuration provider initializes the database when it's empty.</span></span> <span data-ttu-id="4ed55-430">Da [Konfigurationsschlüssel die Groß-/Kleinschreibung nicht beachten](#keys), wird das zum Initialisieren der Datenbank verwendete Wörterbuch mit der Vergleichsfunktion, die die Groß-/Kleinschreibung nicht beachtet, erstellt ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span><span class="sxs-lookup"><span data-stu-id="4ed55-430">Since [configuration keys are case-insensitive](#keys), the dictionary used to initialize the database is created with the case-insensitive comparer ([StringComparer.OrdinalIgnoreCase](xref:System.StringComparer.OrdinalIgnoreCase)).</span></span>

<span data-ttu-id="4ed55-431">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-431">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="4ed55-432">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-432">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="4ed55-433">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-433">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/3.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="4ed55-434">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-434">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples_snippets/3.x/ConfigurationSample/Program.cs?highlight=7-8)]

<a name="acs"></a>

## <a name="access-configuration-in-startup"></a><span data-ttu-id="4ed55-435">Zugriffskonfiguration beim Start</span><span class="sxs-lookup"><span data-stu-id="4ed55-435">Access configuration in Startup</span></span>

<span data-ttu-id="4ed55-436">Der folgende Code zeigt Konfigurationsdaten in `Startup`-Methoden an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-436">The following code displays configuration data in `Startup` methods:</span></span>

[!code-csharp[](index/samples/3.x/ConfigSample/StartupKey.cs?name=snippet&highlight=13,18)]

<span data-ttu-id="4ed55-437">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="4ed55-437">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-no-locrazor-pages"></a><span data-ttu-id="4ed55-438">Zugriffskonfiguration in Razor Pages</span><span class="sxs-lookup"><span data-stu-id="4ed55-438">Access configuration in Razor Pages</span></span>

<span data-ttu-id="4ed55-439">Der folgende Code zeigt Konfigurationsdaten auf einer Razor-Seite an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-439">The following code displays configuration data in a Razor Page:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Pages/Test5.cshtml)]

<span data-ttu-id="4ed55-440">Im folgenden Code wird `MyOptions` mit <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> zum Dienstcontainer hinzugefügt und an die Konfiguration gebunden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-440">In the following code, `MyOptions` is added to the service container with <xref:Microsoft.Extensions.DependencyInjection.OptionsConfigurationServiceCollectionExtensions.Configure*> and bound to configuration:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup3.cs?name=snippet_Example2)]

<span data-ttu-id="4ed55-441">Das folgende Markup verwendet die [`@inject`](xref:mvc/views/razor#inject) Razor Direktive, um die Optionswerte aufzulösen und anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-441">The following markup uses the [`@inject`](xref:mvc/views/razor#inject) Razor directive to resolve and display the options values:</span></span>

[!code-cshtml[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Pages/Test3.cshtml)]

## <a name="access-configuration-in-a-mvc-view-file"></a><span data-ttu-id="4ed55-442">Zugriffskonfiguration in einer MVC-Ansichtsdatei</span><span class="sxs-lookup"><span data-stu-id="4ed55-442">Access configuration in a MVC view file</span></span>

<span data-ttu-id="4ed55-443">Der folgende Code zeigt Konfigurationsdaten in einer MVC-Ansicht an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-443">The following code displays configuration data in a MVC view:</span></span>

[!code-cshtml[](index/samples/3.x/ConfigSample/Views/Home2/Index.cshtml)]

## <a name="configure-options-with-a-delegate"></a><span data-ttu-id="4ed55-444">Konfigurieren von Optionen mit einem Delegaten</span><span class="sxs-lookup"><span data-stu-id="4ed55-444">Configure options with a delegate</span></span>

<span data-ttu-id="4ed55-445">In einem Delegaten konfigurierte Optionen überschreiben die in den Konfigurationsanbietern festgelegten Werte.</span><span class="sxs-lookup"><span data-stu-id="4ed55-445">Options configured in a delegate override values set in the configuration providers.</span></span>

<span data-ttu-id="4ed55-446">Das Konfigurieren von Optionen mit einem Delegaten wird als Beispiel 2 in der Beispiel-App veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="4ed55-446">Configuring options with a delegate is demonstrated as Example 2 in the sample app.</span></span>

<span data-ttu-id="4ed55-447">Im folgenden Code wird ein dritter <xref:Microsoft.Extensions.Options.IConfigureOptions%601>-Dienst zum Dienstcontainer hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-447">In the following code, an <xref:Microsoft.Extensions.Options.IConfigureOptions%601> service is added to the service container.</span></span> <span data-ttu-id="4ed55-448">Er verwendet einen Delegaten, um Werte für `MyOptions` zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="4ed55-448">It uses a delegate to configure values for `MyOptions`:</span></span>

[!code-csharp[](~/fundamentals/configuration/options/samples/3.x/OptionsSample/Startup2.cs?name=snippet_Example2)]

<span data-ttu-id="4ed55-449">Im folgenden Code werden die Optionswerte angezeigt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-449">The following code displays the options values:</span></span>

[!code-csharp[](options/samples/3.x/OptionsSample/Pages/Test2.cshtml.cs?name=snippet)]

<span data-ttu-id="4ed55-450">Im vorherigen Beispiel wurden die Werte von `Option1` und `Option2` in *appsettings.json* angegeben und anschließend vom konfigurierten Delegaten überschrieben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-450">In the preceding example, the values of `Option1` and `Option2` are specified in *appsettings.json* and then overridden by the configured delegate.</span></span>

<a name="hvac"></a>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="4ed55-451">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="4ed55-451">Host versus app configuration</span></span>

<span data-ttu-id="4ed55-452">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-452">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="4ed55-453">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="4ed55-453">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4ed55-454">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-454">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="4ed55-455">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-455">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="4ed55-456">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-456">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

<a name="dhc"></a>

## <a name="default-host-configuration"></a><span data-ttu-id="4ed55-457">Standardhostkonfiguration</span><span class="sxs-lookup"><span data-stu-id="4ed55-457">Default host configuration</span></span>

<span data-ttu-id="4ed55-458">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) finden Sie in der [ASP.NET Core 2.2-Version dieses Themas](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span><span class="sxs-lookup"><span data-stu-id="4ed55-458">For details on the default configuration when using the [Web Host](xref:fundamentals/host/web-host), see the [ASP.NET Core 2.2 version of this topic](/aspnet/core/fundamentals/configuration/?view=aspnetcore-2.2).</span></span>

* <span data-ttu-id="4ed55-459">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-459">Host configuration is provided from:</span></span>
  * <span data-ttu-id="4ed55-460">Umgebungsvariablen mit dem Präfix `DOTNET_` (z. B. `DOTNET_ENVIRONMENT`), die den [Umgebungsvariablen-Konfigurationsanbieter](#environment-variables) verwenden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-460">Environment variables prefixed with `DOTNET_` (for example, `DOTNET_ENVIRONMENT`) using the [Environment Variables configuration provider](#environment-variables).</span></span> <span data-ttu-id="4ed55-461">Das Präfix (`DOTNET_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-461">The prefix (`DOTNET_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="4ed55-462">Befehlszeilenargumente, die den [Befehlszeilen-Konfigurationsanbieter](#command-line-configuration-provider) verwenden</span><span class="sxs-lookup"><span data-stu-id="4ed55-462">Command-line arguments using the [Command-line configuration provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="4ed55-463">Die Webhost-Standardkonfiguration wird eingerichtet (`ConfigureWebHostDefaults`):</span><span class="sxs-lookup"><span data-stu-id="4ed55-463">Web Host default configuration is established (`ConfigureWebHostDefaults`):</span></span>
  * <span data-ttu-id="4ed55-464">Als Webserver wird Kestrel verwendet und mithilfe der Konfigurationsanbieter der App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-464">Kestrel is used as the web server and configured using the app's configuration providers.</span></span>
  * <span data-ttu-id="4ed55-465">Fügen Sie Middleware zum Filtern von Hosts hinzu.</span><span class="sxs-lookup"><span data-stu-id="4ed55-465">Add Host Filtering Middleware.</span></span>
  * <span data-ttu-id="4ed55-466">Fügen Sie Middleware für weitergeleitete Header hinzu, wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="4ed55-466">Add Forwarded Headers Middleware if the `ASPNETCORE_FORWARDEDHEADERS_ENABLED` environment variable is set to `true`.</span></span>
  * <span data-ttu-id="4ed55-467">Aktivieren Sie die IIS-Integration.</span><span class="sxs-lookup"><span data-stu-id="4ed55-467">Enable IIS integration.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="4ed55-468">Sonstige Konfiguration</span><span class="sxs-lookup"><span data-stu-id="4ed55-468">Other configuration</span></span>

<span data-ttu-id="4ed55-469">Dieses Thema bezieht sich nur auf *App-Konfigurationen*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-469">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="4ed55-470">Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-470">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="4ed55-471">*launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-471">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="4ed55-472">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-472">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="4ed55-473">In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-473">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="4ed55-474">*web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="4ed55-474">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="4ed55-475">Umgebungsvariablen, die in der Datei *launchSettings.json* festgelegt sind, überschreiben diejenigen, die in der Systemumgebung festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="4ed55-475">Environment variables set in *launchSettings.json* override those set in the system environment.</span></span>

<span data-ttu-id="4ed55-476">Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-476">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="4ed55-477">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="4ed55-477">Add configuration from an external assembly</span></span>

<span data-ttu-id="4ed55-478">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="4ed55-478">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="4ed55-479">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-479">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ed55-480">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4ed55-480">Additional resources</span></span>

* [<span data-ttu-id="4ed55-481">Quellcode der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="4ed55-481">Configuration source code</span></span>](https://github.com/dotnet/extensions/tree/master/src/Configuration)
* <xref:fundamentals/configuration/options>
* <xref:blazor/fundamentals/configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4ed55-482">Die App-Konfiguration in ASP.NET Core basiert auf Schlüssel-Wert-Paaren, die von *Konfigurationsanbietern* erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-482">App configuration in ASP.NET Core is based on key-value pairs established by *configuration providers*.</span></span> <span data-ttu-id="4ed55-483">Konfigurationsanbieter lesen Konfigurationsdaten in Schlüssel-Wert-Paare aus verschiedenen Konfigurationsquellen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-483">Configuration providers read configuration data into key-value pairs from a variety of configuration sources:</span></span>

* <span data-ttu-id="4ed55-484">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4ed55-484">Azure Key Vault</span></span>
* <span data-ttu-id="4ed55-485">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4ed55-485">Azure App Configuration</span></span>
* <span data-ttu-id="4ed55-486">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="4ed55-486">Command-line arguments</span></span>
* <span data-ttu-id="4ed55-487">Benutzerdefinierte Anbieter (installiert oder erstellt)</span><span class="sxs-lookup"><span data-stu-id="4ed55-487">Custom providers (installed or created)</span></span>
* <span data-ttu-id="4ed55-488">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="4ed55-488">Directory files</span></span>
* <span data-ttu-id="4ed55-489">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="4ed55-489">Environment variables</span></span>
* <span data-ttu-id="4ed55-490">Speicherinterne .NET Objekte</span><span class="sxs-lookup"><span data-stu-id="4ed55-490">In-memory .NET objects</span></span>
* <span data-ttu-id="4ed55-491">Einstellungsdateien</span><span class="sxs-lookup"><span data-stu-id="4ed55-491">Settings files</span></span>

<span data-ttu-id="4ed55-492">Konfigurationspakete für gängige Konfigurationsanbieterszenarien ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) sind im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-492">Configuration packages for common configuration provider scenarios ([Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration/)) are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="4ed55-493">Codebeispiele, die den <xref:Microsoft.Extensions.Configuration>-Namespace befolgen und in der Beispiel-App verwenden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-493">Code examples that follow and in the sample app use the <xref:Microsoft.Extensions.Configuration> namespace:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="4ed55-494">Das *Optionsmuster* ist eine Erweiterung der in diesem Thema beschriebenen Konfigurationskonzepte.</span><span class="sxs-lookup"><span data-stu-id="4ed55-494">The *options pattern* is an extension of the configuration concepts described in this topic.</span></span> <span data-ttu-id="4ed55-495">Optionen verwenden Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-495">Options use classes to represent groups of related settings.</span></span> <span data-ttu-id="4ed55-496">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-496">For more information, see <xref:fundamentals/configuration/options>.</span></span>

<span data-ttu-id="4ed55-497">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4ed55-497">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/configuration/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="host-versus-app-configuration"></a><span data-ttu-id="4ed55-498">Hostkonfiguration und App-Konfiguration im Vergleich</span><span class="sxs-lookup"><span data-stu-id="4ed55-498">Host versus app configuration</span></span>

<span data-ttu-id="4ed55-499">Bevor die App konfiguriert und gestartet wird, wird ein *Host* konfiguriert und gestartet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-499">Before the app is configured and started, a *host* is configured and launched.</span></span> <span data-ttu-id="4ed55-500">Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="4ed55-500">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="4ed55-501">Die App und der Host werden mit den in diesem Thema beschriebenen Konfigurationsanbietern konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-501">Both the app and the host are configured using the configuration providers described in this topic.</span></span> <span data-ttu-id="4ed55-502">Schlüssel-Wert-Paare der Hostkonfiguration sind ebenfalls in der globalen App-Konfiguration enthalten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-502">Host configuration key-value pairs are also included in the app's configuration.</span></span> <span data-ttu-id="4ed55-503">Weitere Informationen dazu, wie Konfigurationsanbieter beim Erstellen des Hosts verwendet werden, und wie sich Konfigurationsquellen auf die Hostkonfiguration auswirken, finden Sie unter <xref:fundamentals/index#host>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-503">For more information on how the configuration providers are used when the host is built and how configuration sources affect host configuration, see <xref:fundamentals/index#host>.</span></span>

## <a name="other-configuration"></a><span data-ttu-id="4ed55-504">Sonstige Konfiguration</span><span class="sxs-lookup"><span data-stu-id="4ed55-504">Other configuration</span></span>

<span data-ttu-id="4ed55-505">Dieses Thema bezieht sich nur auf *App-Konfigurationen*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-505">This topic only pertains to *app configuration*.</span></span> <span data-ttu-id="4ed55-506">Andere Aspekte des Ausführens und Hostings von ASP.NET Core-Apps werden mithilfe von Konfigurationsdateien konfiguriert, die in diesem Thema nicht behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-506">Other aspects of running and hosting ASP.NET Core apps are configured using configuration files not covered in this topic:</span></span>

* <span data-ttu-id="4ed55-507">*launch.json*/*launchSettings.json* sind Toolkonfigurationsdateien für die Entwicklungsumgebung, die hier beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-507">*launch.json*/*launchSettings.json* are tooling configuration files for the Development environment, described:</span></span>
  * <span data-ttu-id="4ed55-508">In <xref:fundamentals/environments#development>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-508">In <xref:fundamentals/environments#development>.</span></span>
  * <span data-ttu-id="4ed55-509">In der Dokumentationsreihe, wo die Dateien zum Konfigurieren von ASP.NET Core-Apps für Entwicklungsszenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-509">Across the documentation set where the files are used to configure ASP.NET Core apps for Development scenarios.</span></span>
* <span data-ttu-id="4ed55-510">*web.config* ist eine Serverkonfigurationsdatei, die in den folgenden Themen beschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="4ed55-510">*web.config* is a server configuration file, described in the following topics:</span></span>
  * <xref:host-and-deploy/iis/index>
  * <xref:host-and-deploy/aspnet-core-module>

<span data-ttu-id="4ed55-511">Weitere Informationen zum Migrieren der App-Konfiguration aus früheren Versionen von ASP.NET finden Sie unter <xref:migration/proper-to-2x/index#store-configurations>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-511">For more information on migrating app configuration from earlier versions of ASP.NET, see <xref:migration/proper-to-2x/index#store-configurations>.</span></span>

## <a name="default-configuration"></a><span data-ttu-id="4ed55-512">Standardkonfiguration</span><span class="sxs-lookup"><span data-stu-id="4ed55-512">Default configuration</span></span>

<span data-ttu-id="4ed55-513">Web-Apps, die auf den [dotnet new](/dotnet/core/tools/dotnet-new)-Vorlagen von ASP.NET Core basieren, rufen beim Erstellen eines Hosts <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf.</span><span class="sxs-lookup"><span data-stu-id="4ed55-513">Web apps based on the ASP.NET Core [dotnet new](/dotnet/core/tools/dotnet-new) templates call <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> when building a host.</span></span> <span data-ttu-id="4ed55-514">`CreateDefaultBuilder` legt die Standardkonfiguration für die App in der folgenden Reihenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="4ed55-514">`CreateDefaultBuilder` provides default configuration for the app in the following order:</span></span>

<span data-ttu-id="4ed55-515">Folgendes gilt für Apps, die den [Webhost](xref:fundamentals/host/web-host) verwenden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-515">The following applies to apps using the [Web Host](xref:fundamentals/host/web-host).</span></span> <span data-ttu-id="4ed55-516">Ausführliche Informationen zur Standardkonfiguration bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) finden Sie in der [neuesten Version dieses Themas](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="4ed55-516">For details on the default configuration when using the [Generic Host](xref:fundamentals/host/generic-host), see the [latest version of this topic](xref:fundamentals/configuration/index).</span></span>

* <span data-ttu-id="4ed55-517">Die Konfiguration des Hosts wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-517">Host configuration is provided from:</span></span>
  * <span data-ttu-id="4ed55-518">Umgebungsvariablen mit dem Präfix `ASPNETCORE_` (zum Beispiel `ASPNETCORE_ENVIRONMENT`) mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4ed55-518">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`) using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span> <span data-ttu-id="4ed55-519">Das Präfix (`ASPNETCORE_`) wird beim Laden der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-519">The prefix (`ASPNETCORE_`) is stripped when the configuration key-value pairs are loaded.</span></span>
  * <span data-ttu-id="4ed55-520">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4ed55-520">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>
* <span data-ttu-id="4ed55-521">Die App-Konfiguration wird durch Folgendes festgelegt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-521">App configuration is provided from:</span></span>
  * <span data-ttu-id="4ed55-522">*appsettings.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4ed55-522">*appsettings.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="4ed55-523">*appsettings.{Umgebung}.json* mithilfe des [Dateikonfigurationsanbieters](#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4ed55-523">*appsettings.{Environment}.json* using the [File Configuration Provider](#file-configuration-provider).</span></span>
  * <span data-ttu-id="4ed55-524">[Geheimnis-Manager](xref:security/app-secrets), wenn die App in der `Development`-Umgebung mit der Einstiegsassembly ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4ed55-524">[Secret Manager](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="4ed55-525">Umgebungsvariablen mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4ed55-525">Environment variables using the [Environment Variables Configuration Provider](#environment-variables-configuration-provider).</span></span>
  * <span data-ttu-id="4ed55-526">Befehlszeilenargumente mithilfe des [Befehlszeilen-Konfigurationsanbieters](#command-line-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="4ed55-526">Command-line arguments using the [Command-line Configuration Provider](#command-line-configuration-provider).</span></span>

## <a name="security"></a><span data-ttu-id="4ed55-527">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="4ed55-527">Security</span></span>

<span data-ttu-id="4ed55-528">Wenden Sie die folgenden Verfahren an, um vertrauliche Konfigurationsdaten zu schützen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-528">Adopt the following practices to secure sensitive configuration data:</span></span>

* <span data-ttu-id="4ed55-529">Speichern Sie nie Kennwörter oder andere vertrauliche Daten im Konfigurationsanbietercode oder in Nur-Text-Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="4ed55-529">Never store passwords or other sensitive data in configuration provider code or in plain text configuration files.</span></span>
* <span data-ttu-id="4ed55-530">Verwenden Sie keine Produktionsgeheimnisse in Entwicklungs- oder Testumgebungen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-530">Don't use production secrets in development or test environments.</span></span>
* <span data-ttu-id="4ed55-531">Geben Sie Geheimnisse außerhalb des Projekts an, damit sie nicht versehentlich in ein Quellcoderepository übernommen werden können.</span><span class="sxs-lookup"><span data-stu-id="4ed55-531">Specify secrets outside of the project so that they can't be accidentally committed to a source code repository.</span></span>

<span data-ttu-id="4ed55-532">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-532">For more information, see the following topics:</span></span>

* <xref:fundamentals/environments>
* <span data-ttu-id="4ed55-533"><xref:security/app-secrets>: Enthält Hinweise zur Verwendung von Umgebungsvariablen für das Speichern vertraulicher Daten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-533"><xref:security/app-secrets>: Includes advice on using environment variables to store sensitive data.</span></span> <span data-ttu-id="4ed55-534">Der Geheimnis-Manager verwendet den Dateikonfigurationsanbieter, um Benutzergeheimnisse in einer JSON-Datei im lokalen System zu speichern.</span><span class="sxs-lookup"><span data-stu-id="4ed55-534">The Secret Manager uses the File Configuration Provider to store user secrets in a JSON file on the local system.</span></span> <span data-ttu-id="4ed55-535">Der Dateikonfigurationsanbieter wird später in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-535">The File Configuration Provider is described later in this topic.</span></span>

<span data-ttu-id="4ed55-536">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) speichert App-Geheimnisse für ASP.NET Core-Apps auf sichere Weise.</span><span class="sxs-lookup"><span data-stu-id="4ed55-536">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) safely stores app secrets for ASP.NET Core apps.</span></span> <span data-ttu-id="4ed55-537">Weitere Informationen finden Sie unter <xref:security/key-vault-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-537">For more information, see <xref:security/key-vault-configuration>.</span></span>

## <a name="hierarchical-configuration-data"></a><span data-ttu-id="4ed55-538">Hierarchische Konfigurationsdaten</span><span class="sxs-lookup"><span data-stu-id="4ed55-538">Hierarchical configuration data</span></span>

<span data-ttu-id="4ed55-539">Die Konfigurations-API kann hierarchische Konfigurationsdaten erhalten, indem sie die hierarchischen Daten mit einem Trennzeichen in den Konfigurationsschlüsseln vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="4ed55-539">The Configuration API is capable of maintaining hierarchical configuration data by flattening the hierarchical data with the use of a delimiter in the configuration keys.</span></span>

<span data-ttu-id="4ed55-540">Die folgende JSON-Datei enthält vier Schlüssel in einer strukturierten Hierarchie von zwei Abschnitten:</span><span class="sxs-lookup"><span data-stu-id="4ed55-540">In the following JSON file, four keys exist in a structured hierarchy of two sections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  }
}
```

<span data-ttu-id="4ed55-541">Wenn die Datei in die Konfiguration gelesen wird, werden eindeutige Schlüssel erstellt, um die ursprüngliche hierarchische Datenstruktur der Konfigurationsquelle zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-541">When the file is read into configuration, unique keys are created to maintain the original hierarchical data structure of the configuration source.</span></span> <span data-ttu-id="4ed55-542">Die Abschnitte und Schlüssel werden mithilfe eines Doppelpunkts (`:`) vereinfacht, um die Originalstruktur zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="4ed55-542">The sections and keys are flattened with the use of a colon (`:`) to maintain the original structure:</span></span>

* <span data-ttu-id="4ed55-543">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-543">section0:key0</span></span>
* <span data-ttu-id="4ed55-544">section0:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-544">section0:key1</span></span>
* <span data-ttu-id="4ed55-545">section1:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-545">section1:key0</span></span>
* <span data-ttu-id="4ed55-546">section1:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-546">section1:key1</span></span>

<span data-ttu-id="4ed55-547">Mit den Methoden <xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> und <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> können Abschnitte und untergeordnete Abschnittelemente in den Konfigurationsdaten isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-547"><xref:Microsoft.Extensions.Configuration.ConfigurationSection.GetSection*> and <xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*> methods are available to isolate sections and children of a section in the configuration data.</span></span> <span data-ttu-id="4ed55-548">Diese Methoden werden später im Abschnitt [GetSection, GetChildren und Exists](#getsection-getchildren-and-exists) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-548">These methods are described later in [GetSection, GetChildren, and Exists](#getsection-getchildren-and-exists).</span></span>

## <a name="conventions"></a><span data-ttu-id="4ed55-549">Konventionen</span><span class="sxs-lookup"><span data-stu-id="4ed55-549">Conventions</span></span>

### <a name="sources-and-providers"></a><span data-ttu-id="4ed55-550">Quellen und Anbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-550">Sources and providers</span></span>

<span data-ttu-id="4ed55-551">Beim Starten der Anwendung werden Konfigurationsquellen in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="4ed55-551">At app startup, configuration sources are read in the order that their configuration providers are specified.</span></span>

<span data-ttu-id="4ed55-552">Konfigurationsanbieter, die Änderungserkennung implementieren, können Konfigurationen erneut laden, wenn zugrunde liegende Einstellungen geändert werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-552">Configuration providers that implement change detection have the ability to reload configuration when an underlying setting is changed.</span></span> <span data-ttu-id="4ed55-553">Der Dateikonfigurationsanbieter (weiter unten in diesem Thema beschrieben) und der [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) implementieren beispielsweise die Änderungserkennung.</span><span class="sxs-lookup"><span data-stu-id="4ed55-553">For example, the File Configuration Provider (described later in this topic) and the [Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) implement change detection.</span></span>

<span data-ttu-id="4ed55-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> steht im App-Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="4ed55-554"><xref:Microsoft.Extensions.Configuration.IConfiguration> is available in the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="4ed55-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> kann in <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> von Razor Pages oder <xref:Microsoft.AspNetCore.Mvc.Controller> von MVC eingefügt werden, um die Konfiguration für die Klasse abzurufen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-555"><xref:Microsoft.Extensions.Configuration.IConfiguration> can be injected into a Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> or MVC <xref:Microsoft.AspNetCore.Mvc.Controller> to obtain configuration for the class.</span></span>

<span data-ttu-id="4ed55-556">In den folgenden Beispielen wird das Feld `_config` verwendet, um auf Konfigurationswerte zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-556">In the following examples, the `_config` field is used to access configuration values:</span></span>

```csharp
public class IndexModel : PageModel
{
    private readonly IConfiguration _config;

    public IndexModel(IConfiguration config)
    {
        _config = config;
    }
}
```

```csharp
public class HomeController : Controller
{
    private readonly IConfiguration _config;

    public HomeController(IConfiguration config)
    {
        _config = config;
    }
}
```

<span data-ttu-id="4ed55-557">Konfigurationsanbieter können die Abhängigkeitsinjektion nicht verwenden, weil sie nicht verfügbar ist, wenn sie vom Host eingerichtet werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-557">Configuration providers can't utilize DI, as it's not available when they're set up by the host.</span></span>

### <a name="keys"></a><span data-ttu-id="4ed55-558">Tasten</span><span class="sxs-lookup"><span data-stu-id="4ed55-558">Keys</span></span>

<span data-ttu-id="4ed55-559">Konfigurationsschlüssel entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-559">Configuration keys adopt the following conventions:</span></span>

* <span data-ttu-id="4ed55-560">Bei Schlüsseln wird die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-560">Keys are case-insensitive.</span></span> <span data-ttu-id="4ed55-561">Beispielsweise verweisen `ConnectionString` und `connectionstring` auf denselben Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="4ed55-561">For example, `ConnectionString` and `connectionstring` are treated as equivalent keys.</span></span>
* <span data-ttu-id="4ed55-562">Wenn ein Wert für denselben Schlüssel von denselben oder unterschiedlichen Konfigurationsanbietern festgelegt wird, wird der zuletzt für den Schlüssel bestimmte Wert verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-562">If a value for the same key is set by the same or different configuration providers, the last value set on the key is the value used.</span></span> <span data-ttu-id="4ed55-563">Weitere Informationen zu doppelten JSON-Schlüsseln finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/extensions/issues/2381).</span><span class="sxs-lookup"><span data-stu-id="4ed55-563">For more information on duplicate JSON keys, see [this GitHub issue](https://github.com/dotnet/extensions/issues/2381).</span></span>
* <span data-ttu-id="4ed55-564">Hierarchische Schlüssel</span><span class="sxs-lookup"><span data-stu-id="4ed55-564">Hierarchical keys</span></span>
  * <span data-ttu-id="4ed55-565">Innerhalb der Konfigurations-API funktioniert ein Doppelpunkt (`:`) als Trennzeichen auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-565">Within the Configuration API, a colon separator (`:`) works on all platforms.</span></span>
  * <span data-ttu-id="4ed55-566">In Umgebungsvariablen funktioniert ein Doppelpunkt als Trennzeichen ggf. nicht auf allen Plattformen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-566">In environment variables, a colon separator may not work on all platforms.</span></span> <span data-ttu-id="4ed55-567">Ein doppelter Unterstrich (`__`) wird auf allen Plattformen unterstützt und automatisch in einen Doppelpunkt konvertiert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-567">A double underscore (`__`) is supported by all platforms and is automatically converted into a colon.</span></span>
  * <span data-ttu-id="4ed55-568">In Azure Key Vault verwenden hierarchische Schlüssel zwei Bindestriche (`--`) als Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-568">In Azure Key Vault, hierarchical keys use `--` (two dashes) as a separator.</span></span> <span data-ttu-id="4ed55-569">Schreiben Sie Code, um die Bindestriche durch einen Doppelpunkt zu ersetzen, wenn die Geheimnisse in die App-Konfiguration geladen werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-569">Write code to replace the dashes with a colon when the secrets are loaded into the app's configuration.</span></span>
* <span data-ttu-id="4ed55-570"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="4ed55-570">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4ed55-571">Die Arraybindung wird im Abschnitt [Binden eines Arrays an eine Klasse](#bind-an-array-to-a-class) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-571">Array binding is described in the [Bind an array to a class](#bind-an-array-to-a-class) section.</span></span>

### <a name="values"></a><span data-ttu-id="4ed55-572">Werte</span><span class="sxs-lookup"><span data-stu-id="4ed55-572">Values</span></span>

<span data-ttu-id="4ed55-573">Konfigurationswerte entsprechen den folgenden Konventionen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-573">Configuration values adopt the following conventions:</span></span>

* <span data-ttu-id="4ed55-574">Die Werte sind Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-574">Values are strings.</span></span>
* <span data-ttu-id="4ed55-575">NULL-Werte können nicht in einer Konfiguration gespeichert oder an Objekte gebunden werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-575">Null values can't be stored in configuration or bound to objects.</span></span>

## <a name="providers"></a><span data-ttu-id="4ed55-576">Anbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-576">Providers</span></span>

<span data-ttu-id="4ed55-577">Die folgende Tabelle zeigt die für ASP.NET Core-Apps verfügbaren Konfigurationsanbieter.</span><span class="sxs-lookup"><span data-stu-id="4ed55-577">The following table shows the configuration providers available to ASP.NET Core apps.</span></span>

| <span data-ttu-id="4ed55-578">Anbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-578">Provider</span></span> | <span data-ttu-id="4ed55-579">Bereitstellung der Konfiguration über&hellip;</span><span class="sxs-lookup"><span data-stu-id="4ed55-579">Provides configuration from&hellip;</span></span> |
| -------- | ----------------------------------- |
| <span data-ttu-id="4ed55-580">[Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="4ed55-580">[Azure Key Vault Configuration Provider](xref:security/key-vault-configuration) (*Security* topics)</span></span> | <span data-ttu-id="4ed55-581">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4ed55-581">Azure Key Vault</span></span> |
| <span data-ttu-id="4ed55-582">[Azure App Configuration-Anbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="4ed55-582">[Azure App Configuration Provider](/azure/azure-app-configuration/quickstart-aspnet-core-app) (Azure documentation)</span></span> | <span data-ttu-id="4ed55-583">Azure App Configuration</span><span class="sxs-lookup"><span data-stu-id="4ed55-583">Azure App Configuration</span></span> |
| [<span data-ttu-id="4ed55-584">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-584">Command-line Configuration Provider</span></span>](#command-line-configuration-provider) | <span data-ttu-id="4ed55-585">Befehlszeilenparameter</span><span class="sxs-lookup"><span data-stu-id="4ed55-585">Command-line parameters</span></span> |
| [<span data-ttu-id="4ed55-586">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-586">Custom configuration provider</span></span>](#custom-configuration-provider) | <span data-ttu-id="4ed55-587">Benutzerdefinierte Quelle</span><span class="sxs-lookup"><span data-stu-id="4ed55-587">Custom source</span></span> |
| [<span data-ttu-id="4ed55-588">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-588">Environment Variables Configuration Provider</span></span>](#environment-variables-configuration-provider) | <span data-ttu-id="4ed55-589">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="4ed55-589">Environment variables</span></span> |
| [<span data-ttu-id="4ed55-590">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-590">File Configuration Provider</span></span>](#file-configuration-provider) | <span data-ttu-id="4ed55-591">Dateien (INI, JSON, XML)</span><span class="sxs-lookup"><span data-stu-id="4ed55-591">Files (INI, JSON, XML)</span></span> |
| [<span data-ttu-id="4ed55-592">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-592">Key-per-file Configuration Provider</span></span>](#key-per-file-configuration-provider) | <span data-ttu-id="4ed55-593">Verzeichnisdateien</span><span class="sxs-lookup"><span data-stu-id="4ed55-593">Directory files</span></span> |
| [<span data-ttu-id="4ed55-594">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-594">Memory Configuration Provider</span></span>](#memory-configuration-provider) | <span data-ttu-id="4ed55-595">In-Memory-Sammlungen</span><span class="sxs-lookup"><span data-stu-id="4ed55-595">In-memory collections</span></span> |
| <span data-ttu-id="4ed55-596">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (Thema *Sicherheit*)</span><span class="sxs-lookup"><span data-stu-id="4ed55-596">[User secrets (Secret Manager)](xref:security/app-secrets) (*Security* topics)</span></span> | <span data-ttu-id="4ed55-597">Datei im Benutzerprofilverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4ed55-597">File in the user profile directory</span></span> |

<span data-ttu-id="4ed55-598">Konfigurationsquellen werden beim Start in der Reihenfolge gelesen, in der ihre Konfigurationsanbieter angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="4ed55-598">Configuration sources are read in the order that their configuration providers are specified at startup.</span></span> <span data-ttu-id="4ed55-599">Die in diesem Thema beschriebenen Konfigurationsanbieter werden in alphabetischer Reihenfolge beschrieben, nicht in der Reihenfolge, in der der Code sie anordnet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-599">The configuration providers described in this topic are described in alphabetical order, not in the order that the code arranges them.</span></span> <span data-ttu-id="4ed55-600">Ordnen Sie die Konfigurationsanbieter im Code so an, dass sie den Prioritäten für die zugrunde liegenden Konfigurationsquellen entsprechen, die für die App erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="4ed55-600">Order configuration providers in code to suit the priorities for the underlying configuration sources that the app requires.</span></span>

<span data-ttu-id="4ed55-601">Eine typische Konfigurationsanbietersequenz ist:</span><span class="sxs-lookup"><span data-stu-id="4ed55-601">A typical sequence of configuration providers is:</span></span>

1. <span data-ttu-id="4ed55-602">Dateien (*appsettings.json*, *appsettings.{Environment}.json*, wobei `{Environment}` die aktuelle Hostingumgebung der App ist)</span><span class="sxs-lookup"><span data-stu-id="4ed55-602">Files (*appsettings.json*, *appsettings.{Environment}.json*, where `{Environment}` is the app's current hosting environment)</span></span>
1. [<span data-ttu-id="4ed55-603">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="4ed55-603">Azure Key Vault</span></span>](xref:security/key-vault-configuration)
1. <span data-ttu-id="4ed55-604">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) (nur Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="4ed55-604">[User secrets (Secret Manager)](xref:security/app-secrets) (Development environment only)</span></span>
1. <span data-ttu-id="4ed55-605">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="4ed55-605">Environment variables</span></span>
1. <span data-ttu-id="4ed55-606">Befehlszeilenargumente</span><span class="sxs-lookup"><span data-stu-id="4ed55-606">Command-line arguments</span></span>

<span data-ttu-id="4ed55-607">In der Regel werden Befehlszeilen-Konfigurationsanbieter in einer Reihe von Anbietern an letzter Stelle positioniert, damit Befehlszeilenargumente die von anderen Anbietern festgelegte Konfiguration überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="4ed55-607">A common practice is to position the Command-line Configuration Provider last in a series of providers to allow command-line arguments to override configuration set by the other providers.</span></span>

<span data-ttu-id="4ed55-608">Die vorhergehende Anbieterfolge wird verwendet, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="4ed55-608">The preceding sequence of providers is used when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4ed55-609">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="4ed55-609">For more information, see the [Default configuration](#default-configuration) section.</span></span>

## <a name="configure-the-host-builder-with-useconfiguration"></a><span data-ttu-id="4ed55-610">Konfigurieren des Host-Generators mit UseConfiguration</span><span class="sxs-lookup"><span data-stu-id="4ed55-610">Configure the host builder with UseConfiguration</span></span>

<span data-ttu-id="4ed55-611">Um den Host-Generator zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> mit der Konfiguration für den Host-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="4ed55-611">To configure the host builder, call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseConfiguration*> on the host builder with the configuration.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var dict = new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };

    var config = new ConfigurationBuilder()
        .AddInMemoryCollection(dict)
        .Build();

    return WebHost.CreateDefaultBuilder(args)
        .UseConfiguration(config)
        .UseStartup<Startup>();
}
```

## <a name="configureappconfiguration"></a><span data-ttu-id="4ed55-612">ConfigureAppConfiguration</span><span class="sxs-lookup"><span data-stu-id="4ed55-612">ConfigureAppConfiguration</span></span>

<span data-ttu-id="4ed55-613">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfigurationsanbieter der App zusätzlich zu denen anzugeben, die automatisch von `CreateDefaultBuilder` hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-613">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration providers in addition to those added automatically by `CreateDefaultBuilder`:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=20)]

### <a name="override-previous-configuration-with-command-line-arguments"></a><span data-ttu-id="4ed55-614">Überschreiben der vorherigen Konfiguration mit Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="4ed55-614">Override previous configuration with command-line arguments</span></span>

<span data-ttu-id="4ed55-615">Um die App-Konfiguration bereitzustellen, die mit Befehlszeilenargumenten überschrieben werden kann, rufen Sie `AddCommandLine` zuletzt auf:</span><span class="sxs-lookup"><span data-stu-id="4ed55-615">To provide app configuration that can be overridden with command-line arguments, call `AddCommandLine` last:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

### <a name="remove-providers-added-by-createdefaultbuilder"></a><span data-ttu-id="4ed55-616">Entfernen von durch CreateDefaultBuilder hinzugefügten Anbietern</span><span class="sxs-lookup"><span data-stu-id="4ed55-616">Remove providers added by CreateDefaultBuilder</span></span>

<span data-ttu-id="4ed55-617">Rufen Sie zunächst [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) in [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) auf, um die durch `CreateDefaultBuilder` hinzugefügten Anbieter zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-617">To remove the providers added by `CreateDefaultBuilder`, call [Clear](/dotnet/api/system.collections.generic.icollection-1.clear) on the [IConfigurationBuilder.Sources](xref:Microsoft.Extensions.Configuration.IConfigurationBuilder.Sources) first:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.Sources.Clear();
    // Add providers here
})
```

### <a name="consume-configuration-during-app-startup"></a><span data-ttu-id="4ed55-618">Verarbeiten der Konfiguration während des App-Starts</span><span class="sxs-lookup"><span data-stu-id="4ed55-618">Consume configuration during app startup</span></span>

<span data-ttu-id="4ed55-619">Die für die App in der `ConfigureAppConfiguration`-Methode angegebene Konfiguration, einschließlich `Startup.ConfigureServices`, ist während des Starts der App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="4ed55-619">Configuration supplied to the app in `ConfigureAppConfiguration` is available during the app's startup, including `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4ed55-620">Weitere Informationen finden Sie im Abschnitt [Hinzufügen von Konfigurationen aus einer externen Assembly](#access-configuration-during-startup).</span><span class="sxs-lookup"><span data-stu-id="4ed55-620">For more information, see the [Access configuration during startup](#access-configuration-during-startup) section.</span></span>

## <a name="command-line-configuration-provider"></a><span data-ttu-id="4ed55-621">Befehlszeilen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-621">Command-line Configuration Provider</span></span>

<span data-ttu-id="4ed55-622"><xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren des Befehlszeilenarguments.</span><span class="sxs-lookup"><span data-stu-id="4ed55-622">The <xref:Microsoft.Extensions.Configuration.CommandLine.CommandLineConfigurationProvider> loads configuration from command-line argument key-value pairs at runtime.</span></span>

<span data-ttu-id="4ed55-623">Um die Befehlszeilenkonfiguration zu aktivieren, wird die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Erweiterungsmethode für eine Instanz von <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-623">To activate command-line configuration, the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> extension method is called on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4ed55-624">`AddCommandLine` wird automatisch aufgerufen, wenn `CreateDefaultBuilder(string [])` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4ed55-624">`AddCommandLine` is automatically called when `CreateDefaultBuilder(string [])` is called.</span></span> <span data-ttu-id="4ed55-625">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="4ed55-625">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4ed55-626">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="4ed55-626">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4ed55-627">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-627">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="4ed55-628">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="4ed55-628">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4ed55-629">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-629">Environment variables.</span></span>

<span data-ttu-id="4ed55-630">`CreateDefaultBuilder` fügt den Befehlszeilen-Konfigurationsanbieter zuletzt hinzu.</span><span class="sxs-lookup"><span data-stu-id="4ed55-630">`CreateDefaultBuilder` adds the Command-line Configuration Provider last.</span></span> <span data-ttu-id="4ed55-631">Während der Laufzeit übergebene Befehlszeilenargumente überschreiben die von anderen Anbietern festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="4ed55-631">Command-line arguments passed at runtime override configuration set by the other providers.</span></span>

<span data-ttu-id="4ed55-632">`CreateDefaultBuilder` wird aktiv, wenn der Host erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="4ed55-632">`CreateDefaultBuilder` acts when the host is constructed.</span></span> <span data-ttu-id="4ed55-633">Deswegen kann die durch `CreateDefaultBuilder` aktivierte Befehlszeilenkonfiguration die Konfiguration des Hosts beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-633">Therefore, command-line configuration activated by `CreateDefaultBuilder` can affect how the host is configured.</span></span>

<span data-ttu-id="4ed55-634">Für Apps, die auf den ASP.NET Core-Vorlagen basieren, wurde `AddCommandLine` bereits von `CreateDefaultBuilder` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-634">For apps based on the ASP.NET Core templates, `AddCommandLine` has already been called by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4ed55-635">Um weitere Konfigurationsanbieter hinzuzufügen und die Möglichkeit einer Überschreibung der Konfiguration dieser Anbieter durch Befehlszeilenargumente beizubehalten, rufen Sie die zusätzlichen Anbieter in `ConfigureAppConfiguration` und zuletzt `AddCommandLine` auf.</span><span class="sxs-lookup"><span data-stu-id="4ed55-635">To add additional configuration providers and maintain the ability to override configuration from those providers with command-line arguments, call the app's additional providers in `ConfigureAppConfiguration` and call `AddCommandLine` last.</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    // Call other providers here
    config.AddCommandLine(args);
})
```

<span data-ttu-id="4ed55-636">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="4ed55-636">**Example**</span></span>

<span data-ttu-id="4ed55-637">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> enthält.</span><span class="sxs-lookup"><span data-stu-id="4ed55-637">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>.</span></span>

1. <span data-ttu-id="4ed55-638">Öffnen Sie im Projektverzeichnis eine Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="4ed55-638">Open a command prompt in the project's directory.</span></span>
1. <span data-ttu-id="4ed55-639">Geben Sie ein Befehlszeilenargument für den `dotnet run`-Befehl an, `dotnet run CommandLineKey=CommandLineValue`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-639">Supply a command-line argument to the `dotnet run` command, `dotnet run CommandLineKey=CommandLineValue`.</span></span>
1. <span data-ttu-id="4ed55-640">Wenn die App ausgeführt wird, öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-640">After the app is running, open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4ed55-641">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für das an `dotnet run` übergebene Konfigurations-Befehlszeilenargument enthält.</span><span class="sxs-lookup"><span data-stu-id="4ed55-641">Observe that the output contains the key-value pair for the configuration command-line argument provided to `dotnet run`.</span></span>

### <a name="arguments"></a><span data-ttu-id="4ed55-642">Argumente</span><span class="sxs-lookup"><span data-stu-id="4ed55-642">Arguments</span></span>

<span data-ttu-id="4ed55-643">Der Wert muss einem Gleichheitszeichen (`=`) folgen, oder der Schlüssel muss ein Präfix (`--` oder `/`) haben, wenn der Wert einem Leerzeichen folgt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-643">The value must follow an equals sign (`=`), or the key must have a prefix (`--` or `/`) when the value follows a space.</span></span> <span data-ttu-id="4ed55-644">Der Wert ist nicht erforderlich, wenn ein Gleichheitszeichen verwendet wird (z. B. `CommandLineKey=`).</span><span class="sxs-lookup"><span data-stu-id="4ed55-644">The value isn't required if an equals sign is used (for example, `CommandLineKey=`).</span></span>

| <span data-ttu-id="4ed55-645">Schlüsselpräfix</span><span class="sxs-lookup"><span data-stu-id="4ed55-645">Key prefix</span></span>               | <span data-ttu-id="4ed55-646">Beispiel</span><span class="sxs-lookup"><span data-stu-id="4ed55-646">Example</span></span>                                                |
| ------------------------ | ------------------------------------------------------ |
| <span data-ttu-id="4ed55-647">Ohne Präfix</span><span class="sxs-lookup"><span data-stu-id="4ed55-647">No prefix</span></span>                | `CommandLineKey1=value1`                               |
| <span data-ttu-id="4ed55-648">Zwei Gedankenstriche (`--`)</span><span class="sxs-lookup"><span data-stu-id="4ed55-648">Two dashes (`--`)</span></span>        | <span data-ttu-id="4ed55-649">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span><span class="sxs-lookup"><span data-stu-id="4ed55-649">`--CommandLineKey2=value2`, `--CommandLineKey2 value2`</span></span> |
| <span data-ttu-id="4ed55-650">Schrägstrich (`/`)</span><span class="sxs-lookup"><span data-stu-id="4ed55-650">Forward slash (`/`)</span></span>      | <span data-ttu-id="4ed55-651">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span><span class="sxs-lookup"><span data-stu-id="4ed55-651">`/CommandLineKey3=value3`, `/CommandLineKey3 value3`</span></span>   |

<span data-ttu-id="4ed55-652">Kombinieren Sie in einem Befehl nicht Schlüssel-Wert-Paare, die ein Gleichheitszeichen verwenden, mit Schlüssel-Wert-Paaren, die ein Leerzeichen verwenden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-652">Within the same command, don't mix command-line argument key-value pairs that use an equals sign with key-value pairs that use a space.</span></span>

<span data-ttu-id="4ed55-653">Beispielbefehle:</span><span class="sxs-lookup"><span data-stu-id="4ed55-653">Example commands:</span></span>

```dotnetcli
dotnet run CommandLineKey1=value1 --CommandLineKey2=value2 /CommandLineKey3=value3
dotnet run --CommandLineKey1 value1 /CommandLineKey2 value2
dotnet run CommandLineKey1= CommandLineKey2=value2
```

### <a name="switch-mappings"></a><span data-ttu-id="4ed55-654">Switchmappings</span><span class="sxs-lookup"><span data-stu-id="4ed55-654">Switch mappings</span></span>

<span data-ttu-id="4ed55-655">Switchmappings erlauben das Angeben einer Logik zum Ersetzen von Schlüsselnamen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-655">Switch mappings allow key name replacement logic.</span></span> <span data-ttu-id="4ed55-656">Wenn Sie die Konfiguration manuell mit <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> erstellen, können Sie ein Wörterbuch der Switchersetzungen für die <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*>-Methode bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-656">When manually building configuration with a <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>, provide a dictionary of switch replacements to the <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> method.</span></span>

<span data-ttu-id="4ed55-657">Wenn das Switchmappingwörterbuch verwendet wird, wird das Wörterbuch für Schlüssel, die dem von einem Befehlszeilenargument angegebenen Schlüssel entsprechen, überprüft.</span><span class="sxs-lookup"><span data-stu-id="4ed55-657">When the switch mappings dictionary is used, the dictionary is checked for a key that matches the key provided by a command-line argument.</span></span> <span data-ttu-id="4ed55-658">Wenn der Befehlszeilenschlüssel im Wörterbuch gefunden wird, wird der Wörterbuchwert (der Schlüsselersatz) zum Festlegen des Schlüssel-Wert-Paares in der App-Konfiguration zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-658">If the command-line key is found in the dictionary, the dictionary value (the key replacement) is passed back to set the key-value pair into the app's configuration.</span></span> <span data-ttu-id="4ed55-659">Ein Switchmapping ist für jeden Befehlszeilenschlüssel erforderlich, dem ein einzelner Gedankenstrich (`-`) vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="4ed55-659">A switch mapping is required for any command-line key prefixed with a single dash (`-`).</span></span>

<span data-ttu-id="4ed55-660">Regeln für Schlüssel von Switchmappingwörterbüchern:</span><span class="sxs-lookup"><span data-stu-id="4ed55-660">Switch mappings dictionary key rules:</span></span>

* <span data-ttu-id="4ed55-661">Switchmappings müssen mit einem Gedankenstrich (`-`) oder zwei Gedankenstrichen (`--`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-661">Switches must start with a dash (`-`) or double-dash (`--`).</span></span>
* <span data-ttu-id="4ed55-662">Das Switchmappingwörterbuch darf keine doppelten Schlüssel enthalten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-662">The switch mappings dictionary must not contain duplicate keys.</span></span>

<span data-ttu-id="4ed55-663">Erstellen Sie ein Switchmappingwörterbuch.</span><span class="sxs-lookup"><span data-stu-id="4ed55-663">Create a switch mappings dictionary.</span></span> <span data-ttu-id="4ed55-664">Im folgenden Beispiel werden zwei Switchmappings erstellt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-664">In the following example, two switch mappings are created:</span></span>

```csharp
public static readonly Dictionary<string, string> _switchMappings = 
    new Dictionary<string, string>
    {
        { "-CLKey1", "CommandLineKey1" },
        { "-CLKey2", "CommandLineKey2" }
    };
```

<span data-ttu-id="4ed55-665">Wenn der Host erstellt wird, rufen Sie `AddCommandLine` mit dem Switchmappingwörterbuch auf:</span><span class="sxs-lookup"><span data-stu-id="4ed55-665">When the host is built, call `AddCommandLine` with the switch mappings dictionary:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddCommandLine(args, _switchMappings);
})
```

<span data-ttu-id="4ed55-666">Bei Apps, die Switchmappings verwenden, sollten im `CreateDefaultBuilder`-Aufruf keine Argumente übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-666">For apps that use switch mappings, the call to `CreateDefaultBuilder` shouldn't pass arguments.</span></span> <span data-ttu-id="4ed55-667">Der Aufruf von `CreateDefaultBuilder` der `AddCommandLine`-Methode umfasst keine zugeordneten Switches, und das Switchmappingwörterbuch kann nicht an `CreateDefaultBuilder` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-667">The `CreateDefaultBuilder` method's `AddCommandLine` call doesn't include mapped switches, and there's no way to pass the switch mapping dictionary to `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4ed55-668">Die Lösung besteht nicht darin, die Argumente an `CreateDefaultBuilder` zu übergeben, sondern der Methode `AddCommandLine` der `ConfigurationBuilder`-Methode zu erlauben, sowohl die Argumente als auch das Switchmappingwörterbuch zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-668">The solution isn't to pass the arguments to `CreateDefaultBuilder` but instead to allow the `ConfigurationBuilder` method's `AddCommandLine` method to process both the arguments and the switch mapping dictionary.</span></span>

<span data-ttu-id="4ed55-669">Das erstellte Switchmappingwörterbuch enthält die in der folgenden Tabelle gezeigten Daten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-669">After the switch mappings dictionary is created, it contains the data shown in the following table.</span></span>

| <span data-ttu-id="4ed55-670">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="4ed55-670">Key</span></span>       | <span data-ttu-id="4ed55-671">Wert</span><span class="sxs-lookup"><span data-stu-id="4ed55-671">Value</span></span>             |
| --------- | ----------------- |
| `-CLKey1` | `CommandLineKey1` |
| `-CLKey2` | `CommandLineKey2` |

<span data-ttu-id="4ed55-672">Wenn Switches zugeordnete Schlüssel beim Start der App verwendet werden, erhält die Konfiguration den Konfigurationswert auf dem vom Wörterbuch bereitgestellten Schlüssel:</span><span class="sxs-lookup"><span data-stu-id="4ed55-672">If the switch-mapped keys are used when starting the app, configuration receives the configuration value on the key supplied by the dictionary:</span></span>

```dotnetcli
dotnet run -CLKey1=value1 -CLKey2=value2
```

<span data-ttu-id="4ed55-673">Nach dem Ausführen des obigen Befehls enthält die Konfiguration die in der folgenden Tabelle angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="4ed55-673">After running the preceding command, configuration contains the values shown in the following table.</span></span>

| <span data-ttu-id="4ed55-674">Key</span><span class="sxs-lookup"><span data-stu-id="4ed55-674">Key</span></span>               | <span data-ttu-id="4ed55-675">Wert</span><span class="sxs-lookup"><span data-stu-id="4ed55-675">Value</span></span>    |
| ----------------- | -------- |
| `CommandLineKey1` | `value1` |
| `CommandLineKey2` | `value2` |

## <a name="environment-variables-configuration-provider"></a><span data-ttu-id="4ed55-676">Umgebungsvariablen-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-676">Environment Variables Configuration Provider</span></span>

<span data-ttu-id="4ed55-677"><xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-677">The <xref:Microsoft.Extensions.Configuration.EnvironmentVariables.EnvironmentVariablesConfigurationProvider> loads configuration from environment variable key-value pairs at runtime.</span></span>

<span data-ttu-id="4ed55-678">Um die Umgebungsvariablenkonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="4ed55-678">To activate environment variables configuration, call the <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="4ed55-679">[Azure App Service](https://azure.microsoft.com/services/app-service/) ermöglicht Ihnen Umgebungsvariablen im Azure-Portal festzulegen, die die App-Konfiguration mit dem Konfigurationsanbieter für Umgebungsvariablen überschreiben können.</span><span class="sxs-lookup"><span data-stu-id="4ed55-679">[Azure App Service](https://azure.microsoft.com/services/app-service/) permits setting environment variables in the Azure Portal that can override app configuration using the Environment Variables Configuration Provider.</span></span> <span data-ttu-id="4ed55-680">Weitere Informationen finden Sie unter [Azure-Apps: Überschreiben der App-Konfiguration im Azure-Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="4ed55-680">For more information, see [Azure Apps: Override app configuration using the Azure Portal](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal).</span></span>

<span data-ttu-id="4ed55-681">`AddEnvironmentVariables` wird zum Laden von Umgebungsvariablen verwendet, die das Präfix `ASPNETCORE_` für die [Hostkonfiguration](#host-versus-app-configuration) aufweisen, wenn ein neuer Host-Generator mit dem [Webhost](xref:fundamentals/host/web-host) initialisiert und `CreateDefaultBuilder` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4ed55-681">`AddEnvironmentVariables` is used to load environment variables prefixed with `ASPNETCORE_` for [host configuration](#host-versus-app-configuration) when a new host builder is initialized with the [Web Host](xref:fundamentals/host/web-host) and `CreateDefaultBuilder` is called.</span></span> <span data-ttu-id="4ed55-682">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="4ed55-682">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4ed55-683">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="4ed55-683">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4ed55-684">App-Konfiguration über Umgebungsvariablen ohne Präfix durch Aufruf von `AddEnvironmentVariables` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="4ed55-684">App configuration from unprefixed environment variables by calling `AddEnvironmentVariables` without a prefix.</span></span>
* <span data-ttu-id="4ed55-685">Optionale Konfiguration aus den Dateien *appsettings.json* und *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-685">Optional configuration from *appsettings.json* and *appsettings.{Environment}.json* files.</span></span>
* <span data-ttu-id="4ed55-686">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="4ed55-686">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4ed55-687">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="4ed55-687">Command-line arguments.</span></span>

<span data-ttu-id="4ed55-688">Der Umgebungsvariablen-Konfigurationsanbieter wird aufgerufen, nachdem die Konfiguration aus Benutzergeheimnissen und *appsettings*-Dateien erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="4ed55-688">The Environment Variables Configuration Provider is called after configuration is established from user secrets and *appsettings* files.</span></span> <span data-ttu-id="4ed55-689">Das Aufrufen des Anbieters an dieser Stelle ermöglicht den während der Laufzeit gelesenen Umgebungsvariablen, die von Benutzergeheimnissen und *appsettings*-Dateien festgelegte Konfiguration zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-689">Calling the provider in this position allows the environment variables read at runtime to override configuration set by user secrets and *appsettings* files.</span></span>

<span data-ttu-id="4ed55-690">Wenn Sie App-Konfigurationen aus zusätzlichen Umgebungsvariablen bereitstellen müssen, rufen Sie die zusätzlichen Anbieter der App in `ConfigureAppConfiguration` auf, und rufen Sie `AddEnvironmentVariables` mit dem Präfix auf:</span><span class="sxs-lookup"><span data-stu-id="4ed55-690">To provide app configuration from additional environment variables, call the app's additional providers in `ConfigureAppConfiguration` and call `AddEnvironmentVariables` with the prefix:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddEnvironmentVariables(prefix: "PREFIX_");
})
```

<span data-ttu-id="4ed55-691">Rufen Sie `AddEnvironmentVariables` zuletzt auf, um Umgebungsvariablen mit dem angegebenen Präfix zuzulassen, um Werte von anderen Anbietern zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-691">Call `AddEnvironmentVariables` last to allow environment variables with the given prefix to override values from other providers.</span></span>

<span data-ttu-id="4ed55-692">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="4ed55-692">**Example**</span></span>

<span data-ttu-id="4ed55-693">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die einen Aufruf von `AddEnvironmentVariables` enthält.</span><span class="sxs-lookup"><span data-stu-id="4ed55-693">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes a call to `AddEnvironmentVariables`.</span></span>

1. <span data-ttu-id="4ed55-694">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="4ed55-694">Run the sample app.</span></span> <span data-ttu-id="4ed55-695">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-695">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4ed55-696">Beachten Sie, dass die Ausgabe das Schlüssel-Wert-Paar für die Umgebungsvariable `ENVIRONMENT` enthält.</span><span class="sxs-lookup"><span data-stu-id="4ed55-696">Observe that the output contains the key-value pair for the environment variable `ENVIRONMENT`.</span></span> <span data-ttu-id="4ed55-697">Der Wert entspricht der Umgebung, in der die App ausgeführt wird. In der Regel ist das `Development` bei lokaler Ausführung.</span><span class="sxs-lookup"><span data-stu-id="4ed55-697">The value reflects the environment in which the app is running, typically `Development` when running locally.</span></span>

<span data-ttu-id="4ed55-698">Um die Liste der von der App gerenderten Umgebungsvariablen kurz zu halten, filtert die App Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-698">To keep the list of environment variables rendered by the app short, the app filters environment variables.</span></span> <span data-ttu-id="4ed55-699">Weitere Informationen finden Sie in der Datei *Pages/Index.cshtml.cs* der Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="4ed55-699">See the sample app's *Pages/Index.cshtml.cs* file.</span></span>

<span data-ttu-id="4ed55-700">Wenn Sie alle für die App verfügbaren Umgebungsvariablen verfügbar machen möchten, ändern Sie `FilteredConfiguration` in *Pages/Index.cshtml.cs* wie folgt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-700">To expose all of the environment variables available to the app, change the `FilteredConfiguration` in *Pages/Index.cshtml.cs* to the following:</span></span>

```csharp
FilteredConfiguration = _config.AsEnumerable();
```

### <a name="prefixes"></a><span data-ttu-id="4ed55-701">Präfixe</span><span class="sxs-lookup"><span data-stu-id="4ed55-701">Prefixes</span></span>

<span data-ttu-id="4ed55-702">Umgebungsvariablen, die in die Konfiguration der App geladen werden, werden gefiltert, wenn Sie ein Präfix für die Methode `AddEnvironmentVariables` angeben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-702">Environment variables loaded into the app's configuration are filtered when supplying a prefix to the `AddEnvironmentVariables` method.</span></span> <span data-ttu-id="4ed55-703">Um beispielsweise Umgebungsvariablen nach dem Präfix `CUSTOM_` zu filtern, geben Sie das Präfix für den Konfigurationsanbieter an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-703">For example, to filter environment variables on the prefix `CUSTOM_`, supply the prefix to the configuration provider:</span></span>

```csharp
var config = new ConfigurationBuilder()
    .AddEnvironmentVariables("CUSTOM_")
    .Build();
```

<span data-ttu-id="4ed55-704">Das Präfix wird beim Erstellen der Schlüssel-Wert-Paare der Konfiguration entfernt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-704">The prefix is stripped off when the configuration key-value pairs are created.</span></span>

<span data-ttu-id="4ed55-705">Beim Erstellen des Host-Generators wird die Hostkonfiguration durch Umgebungsvariablen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-705">When the host builder is created, host configuration is provided by environment variables.</span></span> <span data-ttu-id="4ed55-706">Weitere Informationen zu dem Präfix, das für diese Umgebungsvariablen verwendet wird, finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="4ed55-706">For more information on the prefix used for these environment variables, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4ed55-707">**Präfixe für Verbindungszeichenfolgen**</span><span class="sxs-lookup"><span data-stu-id="4ed55-707">**Connection string prefixes**</span></span>

<span data-ttu-id="4ed55-708">Die Konfigurations-API verfügt über spezielle Verarbeitungsregeln für vier Umgebungsvariablen für Verbindungszeichenfolgen, die beim Konfigurieren von Azure-Verbindungszeichenfolgen für die App-Umgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-708">The Configuration API has special processing rules for four connection string environment variables involved in configuring Azure connection strings for the app environment.</span></span> <span data-ttu-id="4ed55-709">Umgebungsvariablen mit Präfixen, die in der Tabelle aufgeführt werden, werden in die App geladen, wenn für `AddEnvironmentVariables` kein Präfix angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4ed55-709">Environment variables with the prefixes shown in the table are loaded into the app if no prefix is supplied to `AddEnvironmentVariables`.</span></span>

| <span data-ttu-id="4ed55-710">Präfix für Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="4ed55-710">Connection string prefix</span></span> | <span data-ttu-id="4ed55-711">Anbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-711">Provider</span></span> |
| ------------------------ | -------- |
| `CUSTOMCONNSTR_` | <span data-ttu-id="4ed55-712">Benutzerdefinierter Anbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-712">Custom provider</span></span> |
| `MYSQLCONNSTR_` | [<span data-ttu-id="4ed55-713">MySQL</span><span class="sxs-lookup"><span data-stu-id="4ed55-713">MySQL</span></span>](https://www.mysql.com/) |
| `SQLAZURECONNSTR_` | [<span data-ttu-id="4ed55-714">Azure SQL-Datenbank</span><span class="sxs-lookup"><span data-stu-id="4ed55-714">Azure SQL Database</span></span>](https://azure.microsoft.com/services/sql-database/) |
| `SQLCONNSTR_` | [<span data-ttu-id="4ed55-715">SQL Server</span><span class="sxs-lookup"><span data-stu-id="4ed55-715">SQL Server</span></span>](https://www.microsoft.com/sql-server/) |

<span data-ttu-id="4ed55-716">Wenn eine Umgebungsvariable entdeckt und mit einem der vier Präfixe aus der Tabelle in die Konfiguration geladen wird, tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="4ed55-716">When an environment variable is discovered and loaded into configuration with any of the four prefixes shown in the table:</span></span>

* <span data-ttu-id="4ed55-717">Der Konfigurationsschlüssel wird durch Entfernen des Umgebungsvariablenpräfixes und Hinzufügen eines Konfigurationsschlüsselabschnitts (`ConnectionStrings`) erstellt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-717">The configuration key is created by removing the environment variable prefix and adding a configuration key section (`ConnectionStrings`).</span></span>
* <span data-ttu-id="4ed55-718">Ein neues Konfigurations-Schlüssel-Wert-Paar wird erstellt, das den Datenbankverbindungsanbieter repräsentiert (mit Ausnahme des Präfixes `CUSTOMCONNSTR_`, für das kein Anbieter angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="4ed55-718">A new configuration key-value pair is created that represents the database connection provider (except for `CUSTOMCONNSTR_`, which has no stated provider).</span></span>

| <span data-ttu-id="4ed55-719">Umgebungsvariablenschlüssel</span><span class="sxs-lookup"><span data-stu-id="4ed55-719">Environment variable key</span></span> | <span data-ttu-id="4ed55-720">Konvertierter Konfigurationsschlüssel</span><span class="sxs-lookup"><span data-stu-id="4ed55-720">Converted configuration key</span></span> | <span data-ttu-id="4ed55-721">Anbieterkonfigurationseintrag</span><span class="sxs-lookup"><span data-stu-id="4ed55-721">Provider configuration entry</span></span>                                                    |
| ------------------------ | --------------------------- | ------------------------------------------------------------------------------- |
| `CUSTOMCONNSTR_{KEY} `   | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4ed55-722">Konfigurationseintrag wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-722">Configuration entry not created.</span></span>                                                |
| `MYSQLCONNSTR_{KEY}`     | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4ed55-723">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-723">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4ed55-724">Wert: `MySql.Data.MySqlClient`</span><span class="sxs-lookup"><span data-stu-id="4ed55-724">Value: `MySql.Data.MySqlClient`</span></span> |
| `SQLAZURECONNSTR_{KEY}`  | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4ed55-725">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-725">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4ed55-726">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4ed55-726">Value: `System.Data.SqlClient`</span></span>  |
| `SQLCONNSTR_{KEY}`       | `ConnectionStrings:{KEY}`   | <span data-ttu-id="4ed55-727">Schlüssel: `ConnectionStrings:{KEY}_ProviderName`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-727">Key: `ConnectionStrings:{KEY}_ProviderName`:</span></span><br><span data-ttu-id="4ed55-728">Wert: `System.Data.SqlClient`</span><span class="sxs-lookup"><span data-stu-id="4ed55-728">Value: `System.Data.SqlClient`</span></span>  |

<span data-ttu-id="4ed55-729">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="4ed55-729">**Example**</span></span>

<span data-ttu-id="4ed55-730">Eine benutzerdefinierte Umgebungsvariable einer Verbindungszeichenfolge wird auf dem Server erstellt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-730">A custom connection string environment variable is created on the server:</span></span>

* <span data-ttu-id="4ed55-731">Name: `CUSTOMCONNSTR_ReleaseDB`</span><span class="sxs-lookup"><span data-stu-id="4ed55-731">Name: `CUSTOMCONNSTR_ReleaseDB`</span></span>
* <span data-ttu-id="4ed55-732">Wert: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span><span class="sxs-lookup"><span data-stu-id="4ed55-732">Value: `Data Source=ReleaseSQLServer;Initial Catalog=MyReleaseDB;Integrated Security=True`</span></span>

<span data-ttu-id="4ed55-733">Wenn `IConfiguration` eingefügt und einem Feld namens `_config` zugewiesen wird, sieht der Wert so aus:</span><span class="sxs-lookup"><span data-stu-id="4ed55-733">If `IConfiguration` is injected and assigned to a field named `_config`, read the value:</span></span>

```csharp
_config["ConnectionStrings:ReleaseDB"]
```

## <a name="file-configuration-provider"></a><span data-ttu-id="4ed55-734">Dateikonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-734">File Configuration Provider</span></span>

<span data-ttu-id="4ed55-735"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> ist die Basisklasse für das Laden einer Konfiguration aus dem Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="4ed55-735"><xref:Microsoft.Extensions.Configuration.FileConfigurationProvider> is the base class for loading configuration from the file system.</span></span> <span data-ttu-id="4ed55-736">Die folgenden Konfigurationsanbieter sind für bestimmte Dateitypen vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-736">The following configuration providers are dedicated to specific file types:</span></span>

* [<span data-ttu-id="4ed55-737">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-737">INI Configuration Provider</span></span>](#ini-configuration-provider)
* [<span data-ttu-id="4ed55-738">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-738">JSON Configuration Provider</span></span>](#json-configuration-provider)
* [<span data-ttu-id="4ed55-739">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-739">XML Configuration Provider</span></span>](#xml-configuration-provider)

### <a name="ini-configuration-provider"></a><span data-ttu-id="4ed55-740">INI-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-740">INI Configuration Provider</span></span>

<span data-ttu-id="4ed55-741"><xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der INI-Datei.</span><span class="sxs-lookup"><span data-stu-id="4ed55-741">The <xref:Microsoft.Extensions.Configuration.Ini.IniConfigurationProvider> loads configuration from INI file key-value pairs at runtime.</span></span>

<span data-ttu-id="4ed55-742">Um die INI-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="4ed55-742">To activate INI file configuration, call the <xref:Microsoft.Extensions.Configuration.IniConfigurationExtensions.AddIniFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4ed55-743">Der Doppelpunkt kann in einer INI-Dateikonfiguration als Abschnittstrennzeichen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-743">The colon can be used to as a section delimiter in INI file configuration.</span></span>

<span data-ttu-id="4ed55-744">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-744">Overloads permit specifying:</span></span>

* <span data-ttu-id="4ed55-745">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="4ed55-745">Whether the file is optional.</span></span>
* <span data-ttu-id="4ed55-746">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="4ed55-746">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4ed55-747">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="4ed55-747">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4ed55-748">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="4ed55-748">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddIniFile(
        "config.ini", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4ed55-749">Ein allgemeines Beispiel einer INI-Konfigurationsdatei:</span><span class="sxs-lookup"><span data-stu-id="4ed55-749">A generic example of an INI configuration file:</span></span>

```ini
[section0]
key0=value
key1=value

[section1]
subsection:key=value

[section2:subsection0]
key=value

[section2:subsection1]
key=value
```

<span data-ttu-id="4ed55-750">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-750">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4ed55-751">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-751">section0:key0</span></span>
* <span data-ttu-id="4ed55-752">section0:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-752">section0:key1</span></span>
* <span data-ttu-id="4ed55-753">section1:subsection:key</span><span class="sxs-lookup"><span data-stu-id="4ed55-753">section1:subsection:key</span></span>
* <span data-ttu-id="4ed55-754">section2:subsection0:key</span><span class="sxs-lookup"><span data-stu-id="4ed55-754">section2:subsection0:key</span></span>
* <span data-ttu-id="4ed55-755">section2:subsection1:key</span><span class="sxs-lookup"><span data-stu-id="4ed55-755">section2:subsection1:key</span></span>

### <a name="json-configuration-provider"></a><span data-ttu-id="4ed55-756">JSON-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-756">JSON Configuration Provider</span></span>

<span data-ttu-id="4ed55-757"><xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der JSON-Datei.</span><span class="sxs-lookup"><span data-stu-id="4ed55-757">The <xref:Microsoft.Extensions.Configuration.Json.JsonConfigurationProvider> loads configuration from JSON file key-value pairs during runtime.</span></span>

<span data-ttu-id="4ed55-758">Um die JSON-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="4ed55-758">To activate JSON file configuration, call the <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4ed55-759">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-759">Overloads permit specifying:</span></span>

* <span data-ttu-id="4ed55-760">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="4ed55-760">Whether the file is optional.</span></span>
* <span data-ttu-id="4ed55-761">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="4ed55-761">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4ed55-762">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="4ed55-762">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4ed55-763">`AddJsonFile` wird automatisch zweimal aufgerufen, wenn Sie einen neuen Host-Generator mit `CreateDefaultBuilder` initialisieren.</span><span class="sxs-lookup"><span data-stu-id="4ed55-763">`AddJsonFile` is automatically called twice when a new host builder is initialized with `CreateDefaultBuilder`.</span></span> <span data-ttu-id="4ed55-764">Die Methode wird aufgerufen, um die Konfiguration aus folgenden Dateien zu laden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-764">The method is called to load configuration from:</span></span>

* <span data-ttu-id="4ed55-765">*appsettings.json*: Diese Datei wird zuerst gelesen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-765">*appsettings.json*: This file is read first.</span></span> <span data-ttu-id="4ed55-766">Die Umgebungsversion der Datei kann die Werte der Datei *appsettings.json* überschreiben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-766">The environment version of the file can override the values provided by the *appsettings.json* file.</span></span>
* <span data-ttu-id="4ed55-767">*appsettings.{Environment}.json*: Die Umgebungsversion der Datei wird basierend auf [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*) geladen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-767">*appsettings.{Environment}.json*: The environment version of the file is loaded based on the [IHostingEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.EnvironmentName*).</span></span>

<span data-ttu-id="4ed55-768">Weitere Informationen finden Sie im Abschnitt [Standardkonfiguration](#default-configuration).</span><span class="sxs-lookup"><span data-stu-id="4ed55-768">For more information, see the [Default configuration](#default-configuration) section.</span></span>

<span data-ttu-id="4ed55-769">`CreateDefaultBuilder` lädt außerdem:</span><span class="sxs-lookup"><span data-stu-id="4ed55-769">`CreateDefaultBuilder` also loads:</span></span>

* <span data-ttu-id="4ed55-770">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-770">Environment variables.</span></span>
* <span data-ttu-id="4ed55-771">[Benutzergeheimnisse (Geheimnis-Manager)](xref:security/app-secrets) in der Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="4ed55-771">[User secrets (Secret Manager)](xref:security/app-secrets) in the Development environment.</span></span>
* <span data-ttu-id="4ed55-772">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="4ed55-772">Command-line arguments.</span></span>

<span data-ttu-id="4ed55-773">Der JSON-Konfigurationsanbieter wird zuerst eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-773">The JSON Configuration Provider is established first.</span></span> <span data-ttu-id="4ed55-774">Daher überschreiben Benutzergeheimnisse, Umgebungsvariablen und Befehlszeilenargumente die von *appsettings*-Dateien festgelegte Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="4ed55-774">Therefore, user secrets, environment variables, and command-line arguments override configuration set by the *appsettings* files.</span></span>

<span data-ttu-id="4ed55-775">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um Konfiguration der App für andere Dateien als *appsettings.json* und *appsettings.{Environment}.json* anzugeben:</span><span class="sxs-lookup"><span data-stu-id="4ed55-775">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration for files other than *appsettings.json* and *appsettings.{Environment}.json*:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddJsonFile(
        "config.json", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4ed55-776">**Beispiel**</span><span class="sxs-lookup"><span data-stu-id="4ed55-776">**Example**</span></span>

<span data-ttu-id="4ed55-777">Die Beispiel-App nutzt zum Erstellen des Hosts die statische Hilfsmethode `CreateDefaultBuilder`, die zwei Aufrufe von `AddJsonFile` enthält:</span><span class="sxs-lookup"><span data-stu-id="4ed55-777">The sample app takes advantage of the static convenience method `CreateDefaultBuilder` to build the host, which includes two calls to `AddJsonFile`:</span></span>

* <span data-ttu-id="4ed55-778">Der erste Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-778">The first call to `AddJsonFile` loads configuration from *appsettings.json*:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.json)]

* <span data-ttu-id="4ed55-779">Der zweite Aufruf auf `AddJsonFile` lädt die Konfiguration aus *appsettings.{Environment}.json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-779">The second call to `AddJsonFile` loads configuration from *appsettings.{Environment}.json*.</span></span> <span data-ttu-id="4ed55-780">Die folgende Datei wird für *appsettings.Development.json* in der Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-780">For *appsettings.Development.json* in the sample app, the following file is loaded:</span></span>

  [!code-json[](index/samples/2.x/ConfigurationSample/appsettings.Development.json)]

1. <span data-ttu-id="4ed55-781">Führen Sie die Beispiel-App aus.</span><span class="sxs-lookup"><span data-stu-id="4ed55-781">Run the sample app.</span></span> <span data-ttu-id="4ed55-782">Öffnen Sie einen Browser für die App unter `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-782">Open a browser to the app at `http://localhost:5000`.</span></span>
1. <span data-ttu-id="4ed55-783">Die Ausgabe enthält Schlüssel-Wert-Paare für die Konfiguration basierend auf der Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="4ed55-783">The output contains key-value pairs for the configuration based on the app's environment.</span></span> <span data-ttu-id="4ed55-784">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` ist `Debug`, wenn die App in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4ed55-784">The log level for the key `Logging:LogLevel:Default` is `Debug` when running the app in the Development environment.</span></span>
1. <span data-ttu-id="4ed55-785">Führen Sie die Beispiel-App noch mal in der Produktionsumgebung aus:</span><span class="sxs-lookup"><span data-stu-id="4ed55-785">Run the sample app again in the Production environment:</span></span>
   1. <span data-ttu-id="4ed55-786">Öffnen Sie die Datei *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4ed55-786">Open the *Properties/launchSettings.json* file.</span></span>
   1. <span data-ttu-id="4ed55-787">Ändern Sie im `ConfigurationSample`-Profil den Wert der Umgebungsvariablen `ASPNETCORE_ENVIRONMENT` in `Production`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-787">In the `ConfigurationSample` profile, change the value of the `ASPNETCORE_ENVIRONMENT` environment variable to `Production`.</span></span>
   1. <span data-ttu-id="4ed55-788">Speichern Sie die Datei, und führen Sie die App mit `dotnet run` in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="4ed55-788">Save the file and run the app with `dotnet run` in a command shell.</span></span>
1. <span data-ttu-id="4ed55-789">Die Einstellungen in der *appsettings.Development.json*-Datei setzen die Einstellungen in der Datei *appsettings.json* nicht länger außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="4ed55-789">The settings in the *appsettings.Development.json* no longer override the settings in *appsettings.json*.</span></span> <span data-ttu-id="4ed55-790">Die Protokollebene für den Schlüssel `Logging:LogLevel:Default` lautet `Warning`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-790">The log level for the key `Logging:LogLevel:Default` is `Warning`.</span></span>

### <a name="xml-configuration-provider"></a><span data-ttu-id="4ed55-791">XML-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-791">XML Configuration Provider</span></span>

<span data-ttu-id="4ed55-792"><xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> lädt während der Laufzeit die Konfiguration aus den Schlüssel-Wert-Paaren der XML-Datei.</span><span class="sxs-lookup"><span data-stu-id="4ed55-792">The <xref:Microsoft.Extensions.Configuration.Xml.XmlConfigurationProvider> loads configuration from XML file key-value pairs at runtime.</span></span>

<span data-ttu-id="4ed55-793">Um die XML-Dateikonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="4ed55-793">To activate XML file configuration, call the <xref:Microsoft.Extensions.Configuration.XmlConfigurationExtensions.AddXmlFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4ed55-794">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-794">Overloads permit specifying:</span></span>

* <span data-ttu-id="4ed55-795">Ob die Datei optional ist</span><span class="sxs-lookup"><span data-stu-id="4ed55-795">Whether the file is optional.</span></span>
* <span data-ttu-id="4ed55-796">Ob die Konfiguration bei Dateiänderungen neu geladen wird</span><span class="sxs-lookup"><span data-stu-id="4ed55-796">Whether the configuration is reloaded if the file changes.</span></span>
* <span data-ttu-id="4ed55-797">Dass mit <xref:Microsoft.Extensions.FileProviders.IFileProvider> auf die Datei zugegriffen wird</span><span class="sxs-lookup"><span data-stu-id="4ed55-797">The <xref:Microsoft.Extensions.FileProviders.IFileProvider> used to access the file.</span></span>

<span data-ttu-id="4ed55-798">Der Stammknoten der Konfigurationsdatei wird beim Erstellen der Konfigurations-Schlüssel-Wert-Paare ignoriert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-798">The root node of the configuration file is ignored when the configuration key-value pairs are created.</span></span> <span data-ttu-id="4ed55-799">Geben Sie keine Dokumenttypdefinition (DTD) und keinen Namespace in der Datei an.</span><span class="sxs-lookup"><span data-stu-id="4ed55-799">Don't specify a Document Type Definition (DTD) or namespace in the file.</span></span>

<span data-ttu-id="4ed55-800">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="4ed55-800">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddXmlFile(
        "config.xml", optional: true, reloadOnChange: true);
})
```

<span data-ttu-id="4ed55-801">XML-Konfigurationsdateien können unterschiedliche Elementnamen für wiederholte Abschnitte verwenden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-801">XML configuration files can use distinct element names for repeating sections:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section0>
    <key0>value</key0>
    <key1>value</key1>
  </section0>
  <section1>
    <key0>value</key0>
    <key1>value</key1>
  </section1>
</configuration>
```

<span data-ttu-id="4ed55-802">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-802">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4ed55-803">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-803">section0:key0</span></span>
* <span data-ttu-id="4ed55-804">section0:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-804">section0:key1</span></span>
* <span data-ttu-id="4ed55-805">section1:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-805">section1:key0</span></span>
* <span data-ttu-id="4ed55-806">section1:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-806">section1:key1</span></span>

<span data-ttu-id="4ed55-807">Wiederholte Elemente mit den gleichen Elementnamen funktionieren, wenn das `name`-Attribut zur Unterscheidung der Elemente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="4ed55-807">Repeating elements that use the same element name work if the `name` attribute is used to distinguish the elements:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <section name="section0">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
  <section name="section1">
    <key name="key0">value</key>
    <key name="key1">value</key>
  </section>
</configuration>
```

<span data-ttu-id="4ed55-808">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-808">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4ed55-809">section:section0:key:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-809">section:section0:key:key0</span></span>
* <span data-ttu-id="4ed55-810">section:section0:key:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-810">section:section0:key:key1</span></span>
* <span data-ttu-id="4ed55-811">section:section1:key:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-811">section:section1:key:key0</span></span>
* <span data-ttu-id="4ed55-812">section:section1:key:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-812">section:section1:key:key1</span></span>

<span data-ttu-id="4ed55-813">Mit Attributen können Werte bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-813">Attributes can be used to supply values:</span></span>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <key attribute="value" />
  <section>
    <key attribute="value" />
  </section>
</configuration>
```

<span data-ttu-id="4ed55-814">Die vorherige Konfigurationsdatei lädt die folgenden Schlüssel mit `value`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-814">The previous configuration file loads the following keys with `value`:</span></span>

* <span data-ttu-id="4ed55-815">key:attribute</span><span class="sxs-lookup"><span data-stu-id="4ed55-815">key:attribute</span></span>
* <span data-ttu-id="4ed55-816">section:key:attribute</span><span class="sxs-lookup"><span data-stu-id="4ed55-816">section:key:attribute</span></span>

## <a name="key-per-file-configuration-provider"></a><span data-ttu-id="4ed55-817">Schlüssel-pro-Datei-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-817">Key-per-file Configuration Provider</span></span>

<span data-ttu-id="4ed55-818"><xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> verwendet Verzeichnisdateien als Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="4ed55-818">The <xref:Microsoft.Extensions.Configuration.KeyPerFile.KeyPerFileConfigurationProvider> uses a directory's files as configuration key-value pairs.</span></span> <span data-ttu-id="4ed55-819">Der Schlüssel ist der Dateiname.</span><span class="sxs-lookup"><span data-stu-id="4ed55-819">The key is the file name.</span></span> <span data-ttu-id="4ed55-820">Der Wert enthält den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="4ed55-820">The value contains the file's contents.</span></span> <span data-ttu-id="4ed55-821">Der Schlüssel-pro-Datei-Konfigurationsanbieter wird in Docker-Hostingszenarien verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-821">The Key-per-file Configuration Provider is used in Docker hosting scenarios.</span></span>

<span data-ttu-id="4ed55-822">Um die Schlüssel-pro-Datei-Konfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="4ed55-822">To activate key-per-file configuration, call the <xref:Microsoft.Extensions.Configuration.KeyPerFileConfigurationBuilderExtensions.AddKeyPerFile*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span> <span data-ttu-id="4ed55-823">Der `directoryPath` zu den Dateien muss ein absoluter Pfad sein.</span><span class="sxs-lookup"><span data-stu-id="4ed55-823">The `directoryPath` to the files must be an absolute path.</span></span>

<span data-ttu-id="4ed55-824">Überladungen geben Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-824">Overloads permit specifying:</span></span>

* <span data-ttu-id="4ed55-825">Einen `Action<KeyPerFileConfigurationSource>`-Delegat, der die Quelle konfiguriert</span><span class="sxs-lookup"><span data-stu-id="4ed55-825">An `Action<KeyPerFileConfigurationSource>` delegate that configures the source.</span></span>
* <span data-ttu-id="4ed55-826">Ob das Verzeichnis optional ist; und den Pfad zum Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="4ed55-826">Whether the directory is optional and the path to the directory.</span></span>

<span data-ttu-id="4ed55-827">Der doppelte Unterstrich (`__`) wird als Trennzeichen für Konfigurationsschlüssel in Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-827">The double-underscore (`__`) is used as a configuration key delimiter in file names.</span></span> <span data-ttu-id="4ed55-828">Der Dateiname `Logging__LogLevel__System` erzeugt z.B. den Konfigurationsschlüssel `Logging:LogLevel:System`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-828">For example, the file name `Logging__LogLevel__System` produces the configuration key `Logging:LogLevel:System`.</span></span>

<span data-ttu-id="4ed55-829">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="4ed55-829">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    var path = Path.Combine(
        Directory.GetCurrentDirectory(), "path/to/files");
    config.AddKeyPerFile(directoryPath: path, optional: true);
})
```

## <a name="memory-configuration-provider"></a><span data-ttu-id="4ed55-830">Speicherkonfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-830">Memory Configuration Provider</span></span>

<span data-ttu-id="4ed55-831"><xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> verwendet eine In-Memory-Sammlung für Konfigurations-Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="4ed55-831">The <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationProvider> uses an in-memory collection as configuration key-value pairs.</span></span>

<span data-ttu-id="4ed55-832">Um die In-Memory-Sammlungskonfiguration zu aktivieren, rufen Sie die <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*>-Erweiterungsmethode auf einer <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>-Instanz auf.</span><span class="sxs-lookup"><span data-stu-id="4ed55-832">To activate in-memory collection configuration, call the <xref:Microsoft.Extensions.Configuration.MemoryConfigurationBuilderExtensions.AddInMemoryCollection*> extension method on an instance of <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder>.</span></span>

<span data-ttu-id="4ed55-833">Der Konfigurationsanbieter kann mit `IEnumerable<KeyValuePair<String,String>>` initialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-833">The configuration provider can be initialized with an `IEnumerable<KeyValuePair<String,String>>`.</span></span>

<span data-ttu-id="4ed55-834">Rufen Sie `ConfigureAppConfiguration` beim Erstellen des Hosts auf, um die Konfiguration der App anzugeben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-834">Call `ConfigureAppConfiguration` when building the host to specify the app's configuration.</span></span>

<span data-ttu-id="4ed55-835">Im folgenden Beispiel wird ein Konfigurationswörterbuch erstellt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-835">In the following example, a configuration dictionary is created:</span></span>

```csharp
public static readonly Dictionary<string, string> _dict = 
    new Dictionary<string, string>
    {
        {"MemoryCollectionKey1", "value1"},
        {"MemoryCollectionKey2", "value2"}
    };
```

<span data-ttu-id="4ed55-836">Das Wörterbuch wird mit einem Aufruf von `AddInMemoryCollection` verwendet, um die Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-836">The dictionary is used with a call to `AddInMemoryCollection` to provide the configuration:</span></span>

```csharp
.ConfigureAppConfiguration((hostingContext, config) =>
{
    config.AddInMemoryCollection(_dict);
})
```

## <a name="getvalue"></a><span data-ttu-id="4ed55-837">GetValue</span><span class="sxs-lookup"><span data-stu-id="4ed55-837">GetValue</span></span>

<span data-ttu-id="4ed55-838">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extrahiert einen Einzelwert aus der Konfiguration mit einem angegebenen Schlüssel und konvertiert ihn in den angegebenen Typ, der kein Sammlungstyp ist.</span><span class="sxs-lookup"><span data-stu-id="4ed55-838">[`ConfigurationBinder.GetValue<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.GetValue*) extracts a single value from configuration with a specified key and converts it to the specified noncollection type.</span></span> <span data-ttu-id="4ed55-839">Eine Überladung akzeptiert einen Standardwert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-839">An overload accepts a default value.</span></span>

<span data-ttu-id="4ed55-840">Im Beispiel unten geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4ed55-840">The following example:</span></span>

* <span data-ttu-id="4ed55-841">Der Zeichenfolgenwert aus der Konfiguration wird mit dem Schlüssel `NumberKey` extrahiert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-841">Extracts the string value from configuration with the key `NumberKey`.</span></span> <span data-ttu-id="4ed55-842">Wenn `NumberKey` nicht in den Konfigurationsschlüsseln gefunden wird, wird der Standardwert `99` verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-842">If `NumberKey` isn't found in the configuration keys, the default value of `99` is used.</span></span>
* <span data-ttu-id="4ed55-843">Der Typ für den Wert wird als `int` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-843">Types the value as an `int`.</span></span>
* <span data-ttu-id="4ed55-844">Der Wert wird in der `NumberConfig`-Eigenschaft für die Verwendung durch die Seite gespeichert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-844">Stores the value in the `NumberConfig` property for use by the page.</span></span>

```csharp
public class IndexModel : PageModel
{
    public IndexModel(IConfiguration config)
    {
        _config = config;
    }

    public int NumberConfig { get; private set; }

    public void OnGet()
    {
        NumberConfig = _config.GetValue<int>("NumberKey", 99);
    }
}
```

## <a name="getsection-getchildren-and-exists"></a><span data-ttu-id="4ed55-845">GetSection, GetChildren und Exists</span><span class="sxs-lookup"><span data-stu-id="4ed55-845">GetSection, GetChildren, and Exists</span></span>

<span data-ttu-id="4ed55-846">Betrachten Sie für die folgenden Beispiele die JSON-Datei unten.</span><span class="sxs-lookup"><span data-stu-id="4ed55-846">For the examples that follow, consider the following JSON file.</span></span> <span data-ttu-id="4ed55-847">Vier Schlüssel befinden sich in zwei Abschnitten, von denen einer ein Paar Unterabschnitte enthält:</span><span class="sxs-lookup"><span data-stu-id="4ed55-847">Four keys are found across two sections, one of which includes a pair of subsections:</span></span>

```json
{
  "section0": {
    "key0": "value",
    "key1": "value"
  },
  "section1": {
    "key0": "value",
    "key1": "value"
  },
  "section2": {
    "subsection0" : {
      "key0": "value",
      "key1": "value"
    },
    "subsection1" : {
      "key0": "value",
      "key1": "value"
    }
  }
}
```

<span data-ttu-id="4ed55-848">Wenn die Datei in die Konfiguration gelesen wird, werden die folgenden eindeutigen hierarchischen Schlüssel erstellt, um die Konfigurationswerte zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="4ed55-848">When the file is read into configuration, the following unique hierarchical keys are created to hold the configuration values:</span></span>

* <span data-ttu-id="4ed55-849">section0:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-849">section0:key0</span></span>
* <span data-ttu-id="4ed55-850">section0:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-850">section0:key1</span></span>
* <span data-ttu-id="4ed55-851">section1:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-851">section1:key0</span></span>
* <span data-ttu-id="4ed55-852">section1:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-852">section1:key1</span></span>
* <span data-ttu-id="4ed55-853">section2:subsection0:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-853">section2:subsection0:key0</span></span>
* <span data-ttu-id="4ed55-854">section2:subsection0:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-854">section2:subsection0:key1</span></span>
* <span data-ttu-id="4ed55-855">section2:subsection1:key0</span><span class="sxs-lookup"><span data-stu-id="4ed55-855">section2:subsection1:key0</span></span>
* <span data-ttu-id="4ed55-856">section2:subsection1:key1</span><span class="sxs-lookup"><span data-stu-id="4ed55-856">section2:subsection1:key1</span></span>

### <a name="getsection"></a><span data-ttu-id="4ed55-857">GetSection</span><span class="sxs-lookup"><span data-stu-id="4ed55-857">GetSection</span></span>

<span data-ttu-id="4ed55-858">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extrahiert einen Konfigurationsunterabschnitt mit dem angegebenen Unterabschnittsschlüssel.</span><span class="sxs-lookup"><span data-stu-id="4ed55-858">[IConfiguration.GetSection](xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection*) extracts a configuration subsection with the specified subsection key.</span></span>

<span data-ttu-id="4ed55-859">Um ein <xref:Microsoft.Extensions.Configuration.IConfigurationSection>-Element zurückzugeben, das nur die Schlüssel-Wert-Paare in `section1` enthält, rufen Sie `GetSection` auf, und geben Sie den Abschnittsnamen an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-859">To return an <xref:Microsoft.Extensions.Configuration.IConfigurationSection> containing only the key-value pairs in `section1`, call `GetSection` and supply the section name:</span></span>

```csharp
var configSection = _config.GetSection("section1");
```

<span data-ttu-id="4ed55-860">Das Element `configSection` weist keinen Wert auf, nur einen Schlüssel und einen Pfad.</span><span class="sxs-lookup"><span data-stu-id="4ed55-860">The `configSection` doesn't have a value, only a key and a path.</span></span>

<span data-ttu-id="4ed55-861">Um die Werte für Schlüssel in `section2:subsection0` zu erhalten, rufen Sie `GetSection` auf, und geben Sie den Abschnittspfad an:</span><span class="sxs-lookup"><span data-stu-id="4ed55-861">Similarly, to obtain the values for keys in `section2:subsection0`, call `GetSection` and supply the section path:</span></span>

```csharp
var configSection = _config.GetSection("section2:subsection0");
```

<span data-ttu-id="4ed55-862">`GetSection` gibt nie `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="4ed55-862">`GetSection` never returns `null`.</span></span> <span data-ttu-id="4ed55-863">Wenn kein entsprechender Abschnitt gefunden wird, wird ein leeres `IConfigurationSection`-Element zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-863">If a matching section isn't found, an empty `IConfigurationSection` is returned.</span></span>

<span data-ttu-id="4ed55-864">Wenn `GetSection` einen entsprechenden Abschnitt zurückgibt, wird <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> nicht aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-864">When `GetSection` returns a matching section, <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Value> isn't populated.</span></span> <span data-ttu-id="4ed55-865">Eine Eigenschaft <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> und <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> werden zurückgegeben, wenn der Abschnitt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4ed55-865">A <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Key> and <xref:Microsoft.Extensions.Configuration.IConfigurationSection.Path> are returned when the section exists.</span></span>

### <a name="getchildren"></a><span data-ttu-id="4ed55-866">GetChildren</span><span class="sxs-lookup"><span data-stu-id="4ed55-866">GetChildren</span></span>

<span data-ttu-id="4ed55-867">Ein Aufruf von [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) auf `section2` erhält `IEnumerable<IConfigurationSection>` mit folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-867">A call to [IConfiguration.GetChildren](xref:Microsoft.Extensions.Configuration.IConfiguration.GetChildren*) on `section2` obtains an `IEnumerable<IConfigurationSection>` that includes:</span></span>

* `subsection0`
* `subsection1`

```csharp
var configSection = _config.GetSection("section2");

var children = configSection.GetChildren();
```

### <a name="exists"></a><span data-ttu-id="4ed55-868">Vorhanden</span><span class="sxs-lookup"><span data-stu-id="4ed55-868">Exists</span></span>

<span data-ttu-id="4ed55-869">Verwenden Sie [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) um zu bestimmen, ob ein Konfigurationsabschnitt vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="4ed55-869">Use [ConfigurationExtensions.Exists](xref:Microsoft.Extensions.Configuration.ConfigurationExtensions.Exists*) to determine if a configuration section exists:</span></span>

```csharp
var sectionExists = _config.GetSection("section2:subsection2").Exists();
```

<span data-ttu-id="4ed55-870">Im Falle der Beispieldaten ist `sectionExists``false`, weil in den Konfigurationsdaten kein Abschnitt `section2:subsection2` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4ed55-870">Given the example data, `sectionExists` is `false` because there isn't a `section2:subsection2` section in the configuration data.</span></span>

## <a name="bind-to-an-object-graph"></a><span data-ttu-id="4ed55-871">Binden an ein Objektdiagramm</span><span class="sxs-lookup"><span data-stu-id="4ed55-871">Bind to an object graph</span></span>

<span data-ttu-id="4ed55-872"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> kann ein ganzes POCO-Objektdiagramm binden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-872"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> is capable of binding an entire POCO object graph.</span></span> <span data-ttu-id="4ed55-873">Wie beim Binden eines einfachen Objekts werden nur öffentliche Lese-/Schreibeigenschaften gebunden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-873">As with binding a simple object, only public read/write properties are bound.</span></span>

<span data-ttu-id="4ed55-874">Das Beispiel enthält ein `TvShow`-Modell, dessen Objektdiagramm die Klassen `Metadata` und `Actors` enthält (*Models/TvShow.cs*):</span><span class="sxs-lookup"><span data-stu-id="4ed55-874">The sample contains a `TvShow` model whose object graph includes `Metadata` and `Actors` classes (*Models/TvShow.cs*):</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/TvShow.cs?name=snippet1)]

<span data-ttu-id="4ed55-875">Die Beispiel-App verfügt über eine *tvshow.xml*-Datei mit den Konfigurationsdaten:</span><span class="sxs-lookup"><span data-stu-id="4ed55-875">The sample app has a *tvshow.xml* file containing the configuration data:</span></span>

[!code-xml[](index/samples/2.x/ConfigurationSample/tvshow.xml)]

<span data-ttu-id="4ed55-876">Die Konfiguration wird mit der `Bind`-Methode an das gesamte `TvShow`-Objektdiagramm gebunden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-876">Configuration is bound to the entire `TvShow` object graph with the `Bind` method.</span></span> <span data-ttu-id="4ed55-877">Die gebundene Instanz ist einer Eigenschaft zum Rendern zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-877">The bound instance is assigned to a property for rendering:</span></span>

```csharp
var tvShow = new TvShow();
_config.GetSection("tvshow").Bind(tvShow);
TvShow = tvShow;
```

<span data-ttu-id="4ed55-878">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) bindet den angegebenen Typ und gibt ihn zurück.</span><span class="sxs-lookup"><span data-stu-id="4ed55-878">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) binds and returns the specified type.</span></span> <span data-ttu-id="4ed55-879">`Get<T>` ist praktischer als `Bind`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-879">`Get<T>` is more convenient than using `Bind`.</span></span> <span data-ttu-id="4ed55-880">Der folgende Code zeigt, wie `Get<T>` in Bezug auf das vorherige Beispiel verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="4ed55-880">The following code shows how to use `Get<T>` with the preceding example:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_tvshow)]

## <a name="bind-an-array-to-a-class"></a><span data-ttu-id="4ed55-881">Binden eines Arrays an eine Klasse</span><span class="sxs-lookup"><span data-stu-id="4ed55-881">Bind an array to a class</span></span>

<span data-ttu-id="4ed55-882">*Die Beispiel-App veranschaulicht die in diesem Abschnitt erläuterten Konzepte.*</span><span class="sxs-lookup"><span data-stu-id="4ed55-882">*The sample app demonstrates the concepts explained in this section.*</span></span>

<span data-ttu-id="4ed55-883"><xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> unterstützt das Binden von Arrays an Objekte mit Arrayindizes in Konfigurationsschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="4ed55-883">The <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind*> supports binding arrays to objects using array indices in configuration keys.</span></span> <span data-ttu-id="4ed55-884">Jedes Arrayformat, das ein numerisches Schlüsselsegment (`:0:`, `:1:`, &hellip; `:{n}:`) verfügbar macht, kann ein Array an ein POCO-Klassenarray binden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-884">Any array format that exposes a numeric key segment (`:0:`, `:1:`, &hellip; `:{n}:`) is capable of array binding to a POCO class array.</span></span>

> [!NOTE]
> <span data-ttu-id="4ed55-885">Das Binden ist standardmäßig möglich.</span><span class="sxs-lookup"><span data-stu-id="4ed55-885">Binding is provided by convention.</span></span> <span data-ttu-id="4ed55-886">Benutzerdefinierte Konfigurationsanbieter sind nicht erforderlich, um Arraybindung zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="4ed55-886">Custom configuration providers aren't required to implement array binding.</span></span>

<span data-ttu-id="4ed55-887">**In-Memory-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="4ed55-887">**In-memory array processing**</span></span>

<span data-ttu-id="4ed55-888">Betrachten Sie die Konfigurationsschlüssel und -werte in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="4ed55-888">Consider the configuration keys and values shown in the following table.</span></span>

| <span data-ttu-id="4ed55-889">Key</span><span class="sxs-lookup"><span data-stu-id="4ed55-889">Key</span></span>             | <span data-ttu-id="4ed55-890">Wert</span><span class="sxs-lookup"><span data-stu-id="4ed55-890">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="4ed55-891">array:entries:0</span><span class="sxs-lookup"><span data-stu-id="4ed55-891">array:entries:0</span></span> | <span data-ttu-id="4ed55-892">value0</span><span class="sxs-lookup"><span data-stu-id="4ed55-892">value0</span></span> |
| <span data-ttu-id="4ed55-893">array:entries:1</span><span class="sxs-lookup"><span data-stu-id="4ed55-893">array:entries:1</span></span> | <span data-ttu-id="4ed55-894">value1</span><span class="sxs-lookup"><span data-stu-id="4ed55-894">value1</span></span> |
| <span data-ttu-id="4ed55-895">array:entries:2</span><span class="sxs-lookup"><span data-stu-id="4ed55-895">array:entries:2</span></span> | <span data-ttu-id="4ed55-896">value2</span><span class="sxs-lookup"><span data-stu-id="4ed55-896">value2</span></span> |
| <span data-ttu-id="4ed55-897">array:entries:4</span><span class="sxs-lookup"><span data-stu-id="4ed55-897">array:entries:4</span></span> | <span data-ttu-id="4ed55-898">value4</span><span class="sxs-lookup"><span data-stu-id="4ed55-898">value4</span></span> |
| <span data-ttu-id="4ed55-899">array:entries:5</span><span class="sxs-lookup"><span data-stu-id="4ed55-899">array:entries:5</span></span> | <span data-ttu-id="4ed55-900">value5</span><span class="sxs-lookup"><span data-stu-id="4ed55-900">value5</span></span> |

<span data-ttu-id="4ed55-901">Diese Schlüssel und Werte werden mit dem Speicherkonfigurationsanbieter in die Beispiel-App geladen:</span><span class="sxs-lookup"><span data-stu-id="4ed55-901">These keys and values are loaded in the sample app using the Memory Configuration Provider:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=5-12,22)]

<span data-ttu-id="4ed55-902">Das Array überspringt einen Wert für Index &num;3.</span><span class="sxs-lookup"><span data-stu-id="4ed55-902">The array skips a value for index &num;3.</span></span> <span data-ttu-id="4ed55-903">Der Konfigurationsbinder kann weder NULL-Werte binden noch NULL-Einträge in gebundenen Objekten erstellen. Dies wird deutlich, wenn das Ergebnis der Bindung dieses Arrays an ein Objekt demonstriert wird.</span><span class="sxs-lookup"><span data-stu-id="4ed55-903">The configuration binder isn't capable of binding null values or creating null entries in bound objects, which becomes clear in a moment when the result of binding this array to an object is demonstrated.</span></span>

<span data-ttu-id="4ed55-904">In der Beispiel-App ist eine POCO-Klasse verfügbar, um die gebundenen Konfigurationsdaten zu enthalten:</span><span class="sxs-lookup"><span data-stu-id="4ed55-904">In the sample app, a POCO class is available to hold the bound configuration data:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/ArrayExample.cs?name=snippet1)]

<span data-ttu-id="4ed55-905">Die Konfigurationsdaten werden an das Objekt gebunden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-905">The configuration data is bound to the object:</span></span>

```csharp
var arrayExample = new ArrayExample();
_config.GetSection("array").Bind(arrayExample);
```

<span data-ttu-id="4ed55-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*)-Syntax kann ebenfalls verwendet werden, was zu kompakteren Code führt:</span><span class="sxs-lookup"><span data-stu-id="4ed55-906">[`ConfigurationBinder.Get<T>`](xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Get*) syntax can also be used, which results in more compact code:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Pages/Index.cshtml.cs?name=snippet_array)]

<span data-ttu-id="4ed55-907">Das gebundene Objekt, eine Instanz von `ArrayExample`, empfängt die Arraydaten aus der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="4ed55-907">The bound object, an instance of `ArrayExample`, receives the array data from configuration.</span></span>

| <span data-ttu-id="4ed55-908">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="4ed55-908">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="4ed55-909">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="4ed55-909">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="4ed55-910">0</span><span class="sxs-lookup"><span data-stu-id="4ed55-910">0</span></span>                            | <span data-ttu-id="4ed55-911">value0</span><span class="sxs-lookup"><span data-stu-id="4ed55-911">value0</span></span>                       |
| <span data-ttu-id="4ed55-912">1</span><span class="sxs-lookup"><span data-stu-id="4ed55-912">1</span></span>                            | <span data-ttu-id="4ed55-913">value1</span><span class="sxs-lookup"><span data-stu-id="4ed55-913">value1</span></span>                       |
| <span data-ttu-id="4ed55-914">2</span><span class="sxs-lookup"><span data-stu-id="4ed55-914">2</span></span>                            | <span data-ttu-id="4ed55-915">value2</span><span class="sxs-lookup"><span data-stu-id="4ed55-915">value2</span></span>                       |
| <span data-ttu-id="4ed55-916">3</span><span class="sxs-lookup"><span data-stu-id="4ed55-916">3</span></span>                            | <span data-ttu-id="4ed55-917">value4</span><span class="sxs-lookup"><span data-stu-id="4ed55-917">value4</span></span>                       |
| <span data-ttu-id="4ed55-918">4</span><span class="sxs-lookup"><span data-stu-id="4ed55-918">4</span></span>                            | <span data-ttu-id="4ed55-919">value5</span><span class="sxs-lookup"><span data-stu-id="4ed55-919">value5</span></span>                       |

<span data-ttu-id="4ed55-920">Index &num;3 im gebundenen Objekt enthält die Konfigurationsdaten für den `array:4`-Konfigurationsschlüssel und die Wert für `value4`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-920">Index &num;3 in the bound object holds the configuration data for the `array:4` configuration key and its value of `value4`.</span></span> <span data-ttu-id="4ed55-921">Beim Binden von Konfigurationsdaten, die ein Array enthalten, werden die Arrayindizes in den Konfigurationsschlüsseln lediglich zum Durchlaufen der Konfigurationsdaten beim Erstellen des Objekts verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-921">When configuration data containing an array is bound, the array indices in the configuration keys are merely used to iterate the configuration data when creating the object.</span></span> <span data-ttu-id="4ed55-922">Ein NULL-Wert kann in den Konfigurationsdaten nicht beibehalten werden, und ein NULL-Eintrag wird nicht in einem gebundenen Objekt erstellt, wenn ein Array in Konfigurationsschlüsseln mindestens einen Index überspringt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-922">A null value can't be retained in configuration data, and a null-valued entry isn't created in a bound object when an array in configuration keys skip one or more indices.</span></span>

<span data-ttu-id="4ed55-923">Das fehlende Konfigurationselement für Index &num;3 kann vor dem Binden an die `ArrayExample`Instanz von jedem Konfigurationsanbieter bereitgestellt werden, der das richtige Schlüssel-Wert-Paar in der Konfiguration erzeugt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-923">The missing configuration item for index &num;3 can be supplied before binding to the `ArrayExample` instance by any configuration provider that produces the correct key-value pair in configuration.</span></span> <span data-ttu-id="4ed55-924">Wenn das Beispiel einen zusätzlichen JSON-Konfigurationsanbieter mit dem fehlenden Schlüssel-Wert-Paar enthält, entspricht `ArrayExample.Entries` dem kompletten Konfigurationsarray:</span><span class="sxs-lookup"><span data-stu-id="4ed55-924">If the sample included an additional JSON Configuration Provider with the missing key-value pair, the `ArrayExample.Entries` matches the complete configuration array:</span></span>

<span data-ttu-id="4ed55-925">*missing_value.json*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-925">*missing_value.json*:</span></span>

```json
{
  "array:entries:3": "value3"
}
```

<span data-ttu-id="4ed55-926">In `ConfigureAppConfiguration`:</span><span class="sxs-lookup"><span data-stu-id="4ed55-926">In `ConfigureAppConfiguration`:</span></span>

```csharp
config.AddJsonFile(
    "missing_value.json", optional: false, reloadOnChange: false);
```

<span data-ttu-id="4ed55-927">Das Schlüssel-Wert-Paar in der Tabelle wird in die Konfiguration geladen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-927">The key-value pair shown in the table is loaded into configuration.</span></span>

| <span data-ttu-id="4ed55-928">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="4ed55-928">Key</span></span>             | <span data-ttu-id="4ed55-929">Wert</span><span class="sxs-lookup"><span data-stu-id="4ed55-929">Value</span></span>  |
| :-------------: | :----: |
| <span data-ttu-id="4ed55-930">array:entries:3</span><span class="sxs-lookup"><span data-stu-id="4ed55-930">array:entries:3</span></span> | <span data-ttu-id="4ed55-931">value3</span><span class="sxs-lookup"><span data-stu-id="4ed55-931">value3</span></span> |

<span data-ttu-id="4ed55-932">Wenn die `ArrayExample`-Klasseninstanz gebunden wird, nachdem der JSON-Konfigurationsanbieter den Eintrag für Index &num;3 enthält, enthält das `ArrayExample.Entries`-Array den Wert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-932">If the `ArrayExample` class instance is bound after the JSON Configuration Provider includes the entry for index &num;3, the `ArrayExample.Entries` array includes the value.</span></span>

| <span data-ttu-id="4ed55-933">`ArrayExample.Entries`-Index</span><span class="sxs-lookup"><span data-stu-id="4ed55-933">`ArrayExample.Entries` Index</span></span> | <span data-ttu-id="4ed55-934">`ArrayExample.Entries`-Wert</span><span class="sxs-lookup"><span data-stu-id="4ed55-934">`ArrayExample.Entries` Value</span></span> |
| :--------------------------: | :--------------------------: |
| <span data-ttu-id="4ed55-935">0</span><span class="sxs-lookup"><span data-stu-id="4ed55-935">0</span></span>                            | <span data-ttu-id="4ed55-936">value0</span><span class="sxs-lookup"><span data-stu-id="4ed55-936">value0</span></span>                       |
| <span data-ttu-id="4ed55-937">1</span><span class="sxs-lookup"><span data-stu-id="4ed55-937">1</span></span>                            | <span data-ttu-id="4ed55-938">value1</span><span class="sxs-lookup"><span data-stu-id="4ed55-938">value1</span></span>                       |
| <span data-ttu-id="4ed55-939">2</span><span class="sxs-lookup"><span data-stu-id="4ed55-939">2</span></span>                            | <span data-ttu-id="4ed55-940">value2</span><span class="sxs-lookup"><span data-stu-id="4ed55-940">value2</span></span>                       |
| <span data-ttu-id="4ed55-941">3</span><span class="sxs-lookup"><span data-stu-id="4ed55-941">3</span></span>                            | <span data-ttu-id="4ed55-942">value3</span><span class="sxs-lookup"><span data-stu-id="4ed55-942">value3</span></span>                       |
| <span data-ttu-id="4ed55-943">4</span><span class="sxs-lookup"><span data-stu-id="4ed55-943">4</span></span>                            | <span data-ttu-id="4ed55-944">value4</span><span class="sxs-lookup"><span data-stu-id="4ed55-944">value4</span></span>                       |
| <span data-ttu-id="4ed55-945">5</span><span class="sxs-lookup"><span data-stu-id="4ed55-945">5</span></span>                            | <span data-ttu-id="4ed55-946">value5</span><span class="sxs-lookup"><span data-stu-id="4ed55-946">value5</span></span>                       |

<span data-ttu-id="4ed55-947">**JSON-Arrayverarbeitung**</span><span class="sxs-lookup"><span data-stu-id="4ed55-947">**JSON array processing**</span></span>

<span data-ttu-id="4ed55-948">Wenn eine JSON-Datei ein Array enthält, werden Konfigurationsschlüssel für die Arrayelemente mit einem nullbasierten Abschnittsindex erstellt.</span><span class="sxs-lookup"><span data-stu-id="4ed55-948">If a JSON file contains an array, configuration keys are created for the array elements with a zero-based section index.</span></span> <span data-ttu-id="4ed55-949">In der folgenden Konfigurationsdatei ist `subsection` ein Array:</span><span class="sxs-lookup"><span data-stu-id="4ed55-949">In the following configuration file, `subsection` is an array:</span></span>

[!code-json[](index/samples/2.x/ConfigurationSample/json_array.json)]

<span data-ttu-id="4ed55-950">Der JSON-Konfigurationsanbieter liest die Konfigurationsdaten in die folgenden Schlüssel-Wert-Paare:</span><span class="sxs-lookup"><span data-stu-id="4ed55-950">The JSON Configuration Provider reads the configuration data into the following key-value pairs:</span></span>

| <span data-ttu-id="4ed55-951">Key</span><span class="sxs-lookup"><span data-stu-id="4ed55-951">Key</span></span>                     | <span data-ttu-id="4ed55-952">Wert</span><span class="sxs-lookup"><span data-stu-id="4ed55-952">Value</span></span>  |
| ----------------------- | :----: |
| <span data-ttu-id="4ed55-953">json_array:key</span><span class="sxs-lookup"><span data-stu-id="4ed55-953">json_array:key</span></span>          | <span data-ttu-id="4ed55-954">valueA</span><span class="sxs-lookup"><span data-stu-id="4ed55-954">valueA</span></span> |
| <span data-ttu-id="4ed55-955">json_array:subsection:0</span><span class="sxs-lookup"><span data-stu-id="4ed55-955">json_array:subsection:0</span></span> | <span data-ttu-id="4ed55-956">valueB</span><span class="sxs-lookup"><span data-stu-id="4ed55-956">valueB</span></span> |
| <span data-ttu-id="4ed55-957">json_array:subsection:1</span><span class="sxs-lookup"><span data-stu-id="4ed55-957">json_array:subsection:1</span></span> | <span data-ttu-id="4ed55-958">valueC</span><span class="sxs-lookup"><span data-stu-id="4ed55-958">valueC</span></span> |
| <span data-ttu-id="4ed55-959">json_array:subsection:2</span><span class="sxs-lookup"><span data-stu-id="4ed55-959">json_array:subsection:2</span></span> | <span data-ttu-id="4ed55-960">valueD</span><span class="sxs-lookup"><span data-stu-id="4ed55-960">valueD</span></span> |

<span data-ttu-id="4ed55-961">In der Beispiel-App können die Konfigurations-Schlüssel-Wert-Paare mit dieser POCO-Klasse gebunden werden:</span><span class="sxs-lookup"><span data-stu-id="4ed55-961">In the sample app, the following POCO class is available to bind the configuration key-value pairs:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/JsonArrayExample.cs?name=snippet1)]

<span data-ttu-id="4ed55-962">Nach dem Binden enthält `JsonArrayExample.Key` den Wert `valueA`.</span><span class="sxs-lookup"><span data-stu-id="4ed55-962">After binding, `JsonArrayExample.Key` holds the value `valueA`.</span></span> <span data-ttu-id="4ed55-963">Die Unterabschnittwerte werden in der POCO-Arrayeigenschaft `Subsection` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-963">The subsection values are stored in the POCO array property, `Subsection`.</span></span>

| <span data-ttu-id="4ed55-964">`JsonArrayExample.Subsection`-Index</span><span class="sxs-lookup"><span data-stu-id="4ed55-964">`JsonArrayExample.Subsection` Index</span></span> | <span data-ttu-id="4ed55-965">`JsonArrayExample.Subsection`-Wert</span><span class="sxs-lookup"><span data-stu-id="4ed55-965">`JsonArrayExample.Subsection` Value</span></span> |
| :---------------------------------: | :---------------------------------: |
| <span data-ttu-id="4ed55-966">0</span><span class="sxs-lookup"><span data-stu-id="4ed55-966">0</span></span>                                   | <span data-ttu-id="4ed55-967">valueB</span><span class="sxs-lookup"><span data-stu-id="4ed55-967">valueB</span></span>                              |
| <span data-ttu-id="4ed55-968">1</span><span class="sxs-lookup"><span data-stu-id="4ed55-968">1</span></span>                                   | <span data-ttu-id="4ed55-969">valueC</span><span class="sxs-lookup"><span data-stu-id="4ed55-969">valueC</span></span>                              |
| <span data-ttu-id="4ed55-970">2</span><span class="sxs-lookup"><span data-stu-id="4ed55-970">2</span></span>                                   | <span data-ttu-id="4ed55-971">valueD</span><span class="sxs-lookup"><span data-stu-id="4ed55-971">valueD</span></span>                              |

## <a name="custom-configuration-provider"></a><span data-ttu-id="4ed55-972">Benutzerdefinierter Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="4ed55-972">Custom configuration provider</span></span>

<span data-ttu-id="4ed55-973">Die Beispiel-App veranschaulicht, wie ein Standardkonfigurationsanbieter erstellt wird, der Konfigurations-Schlüssel-Wert-Paare aus einer Datenbank mit [Entity Framework (EF)](/ef/core/) liest.</span><span class="sxs-lookup"><span data-stu-id="4ed55-973">The sample app demonstrates how to create a basic configuration provider that reads configuration key-value pairs from a database using [Entity Framework (EF)](/ef/core/).</span></span>

<span data-ttu-id="4ed55-974">Der Anbieter weist die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="4ed55-974">The provider has the following characteristics:</span></span>

* <span data-ttu-id="4ed55-975">Die EF-In-Memory-Datenbank wird zu Demonstrationszwecken verwendet.</span><span class="sxs-lookup"><span data-stu-id="4ed55-975">The EF in-memory database is used for demonstration purposes.</span></span> <span data-ttu-id="4ed55-976">Um eine Datenbank zu verwenden, die eine Verbindungszeichenfolge benötigt, implementieren Sie einen sekundären `ConfigurationBuilder`, um die Verbindungszeichenfolge aus einem anderen Konfigurationsanbieter anzugeben.</span><span class="sxs-lookup"><span data-stu-id="4ed55-976">To use a database that requires a connection string, implement a secondary `ConfigurationBuilder` to supply the connection string from another configuration provider.</span></span>
* <span data-ttu-id="4ed55-977">Der Anbieter liest eine Datenbanktabelle beim Start in die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="4ed55-977">The provider reads a database table into configuration at startup.</span></span> <span data-ttu-id="4ed55-978">Der Anbieter fragt die Datenbank nicht pro Schlüssel ab.</span><span class="sxs-lookup"><span data-stu-id="4ed55-978">The provider doesn't query the database on a per-key basis.</span></span>
* <span data-ttu-id="4ed55-979">Das erneute Laden bei Änderung ist nicht implementiert. Das heißt, das Aktualisieren der Datenbank nach App-Start hat keine Auswirkungen auf die App-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="4ed55-979">Reload-on-change isn't implemented, so updating the database after the app starts has no effect on the app's configuration.</span></span>

<span data-ttu-id="4ed55-980">Definieren Sie eine `EFConfigurationValue`-Entität zum Speichern von Konfigurationswerten in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4ed55-980">Define an `EFConfigurationValue` entity for storing configuration values in the database.</span></span>

<span data-ttu-id="4ed55-981">*Models/EFConfigurationValue.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-981">*Models/EFConfigurationValue.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Models/EFConfigurationValue.cs?name=snippet1)]

<span data-ttu-id="4ed55-982">Fügen Sie `EFConfigurationContext` hinzu, um die konfigurierten Werte zu speichern und auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-982">Add an `EFConfigurationContext` to store and access the configured values.</span></span>

<span data-ttu-id="4ed55-983">*EFConfigurationProvider/EFConfigurationContext.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-983">*EFConfigurationProvider/EFConfigurationContext.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationContext.cs?name=snippet1)]

<span data-ttu-id="4ed55-984">Erstellen Sie eine Klasse, die das <xref:Microsoft.Extensions.Configuration.IConfigurationSource> implementiert.</span><span class="sxs-lookup"><span data-stu-id="4ed55-984">Create a class that implements <xref:Microsoft.Extensions.Configuration.IConfigurationSource>.</span></span>

<span data-ttu-id="4ed55-985">*EFConfigurationProvider/EFConfigurationSource.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-985">*EFConfigurationProvider/EFConfigurationSource.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationSource.cs?name=snippet1)]

<span data-ttu-id="4ed55-986">Erstellen Sie den benutzerdefinierten Konfigurationsanbieter durch Vererbung von <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-986">Create the custom configuration provider by inheriting from <xref:Microsoft.Extensions.Configuration.ConfigurationProvider>.</span></span> <span data-ttu-id="4ed55-987">Der Konfigurationsanbieter initialisiert die Datenbank, wenn diese leer ist.</span><span class="sxs-lookup"><span data-stu-id="4ed55-987">The configuration provider initializes the database when it's empty.</span></span>

<span data-ttu-id="4ed55-988">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-988">*EFConfigurationProvider/EFConfigurationProvider.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/EFConfigurationProvider/EFConfigurationProvider.cs?name=snippet1)]

<span data-ttu-id="4ed55-989">Mit einer `AddEFConfiguration`-Erweiterungsmethode kann die Konfigurationsquelle `ConfigurationBuilder` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="4ed55-989">An `AddEFConfiguration` extension method permits adding the configuration source to a `ConfigurationBuilder`.</span></span>

<span data-ttu-id="4ed55-990">*Extensions/EntityFrameworkExtensions.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-990">*Extensions/EntityFrameworkExtensions.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Extensions/EntityFrameworkExtensions.cs?name=snippet1)]

<span data-ttu-id="4ed55-991">Der folgende Code veranschaulicht die Verwendung des benutzerdefinierten Anbieters `EFConfigurationProvider` in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4ed55-991">The following code shows how to use the custom `EFConfigurationProvider` in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/ConfigurationSample/Program.cs?name=snippet_Program&highlight=29-30)]

## <a name="access-configuration-during-startup"></a><span data-ttu-id="4ed55-992">Zugreifen auf die Konfiguration während des Starts</span><span class="sxs-lookup"><span data-stu-id="4ed55-992">Access configuration during startup</span></span>

<span data-ttu-id="4ed55-993">Fügen Sie `IConfiguration` in den `Startup`-Konstruktor ein, um auf Konfigurationswerte in `Startup.ConfigureServices` zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="4ed55-993">Inject `IConfiguration` into the `Startup` constructor to access configuration values in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4ed55-994">Um auf die Konfiguration in `Startup.Configure` zuzugreifen, fügen Sie entweder `IConfiguration` direkt in die Methode ein, oder verwenden Sie die Instanz aus dem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="4ed55-994">To access configuration in `Startup.Configure`, either inject `IConfiguration` directly into the method or use the instance from the constructor:</span></span>

```csharp
public class Startup
{
    private readonly IConfiguration _config;

    public Startup(IConfiguration config)
    {
        _config = config;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        var value = _config["key"];
    }

    public void Configure(IApplicationBuilder app, IConfiguration config)
    {
        var value = config["key"];
    }
}
```

<span data-ttu-id="4ed55-995">Ein Beispiel für den Zugriff auf die Konfiguration mit den Starthilfsmethoden finden Sie unter [Anwendungsstart: Hilfsmethoden](xref:fundamentals/startup#convenience-methods).</span><span class="sxs-lookup"><span data-stu-id="4ed55-995">For an example of accessing configuration using startup convenience methods, see [App startup: Convenience methods](xref:fundamentals/startup#convenience-methods).</span></span>

## <a name="access-configuration-in-a-no-locrazor-pages-page-or-mvc-view"></a><span data-ttu-id="4ed55-996">Zugreifen auf die Konfiguration auf einer Razor Pages-Seite oder in einer MVC-Ansicht</span><span class="sxs-lookup"><span data-stu-id="4ed55-996">Access configuration in a Razor Pages page or MVC view</span></span>

<span data-ttu-id="4ed55-997">Um auf die Konfigurationseinstellungen auf einer Razor Pages-Seite oder in einer MVC-Ansicht zuzugreifen, fügen Sie eine [using-Anweisung](xref:mvc/views/razor#using) ([C#-Referenz: using-Anweisung](/dotnet/csharp/language-reference/keywords/using-directive)) für den [Microsoft.Extensions.Configuration-Namespace](xref:Microsoft.Extensions.Configuration) hinzu, und fügen Sie <xref:Microsoft.Extensions.Configuration.IConfiguration> auf der Seite bzw. in der Ansicht ein.</span><span class="sxs-lookup"><span data-stu-id="4ed55-997">To access configuration settings in a Razor Pages page or an MVC view, add a [using directive](xref:mvc/views/razor#using) ([C# reference: using directive](/dotnet/csharp/language-reference/keywords/using-directive)) for the [Microsoft.Extensions.Configuration namespace](xref:Microsoft.Extensions.Configuration) and inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into the page or view.</span></span>

<span data-ttu-id="4ed55-998">Auf einer Razor Pages-Seite:</span><span class="sxs-lookup"><span data-stu-id="4ed55-998">In a Razor Pages page:</span></span>

```cshtml
@page
@model IndexModel
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index Page</title>
</head>
<body>
    <h1>Access configuration in a Razor Pages page</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

<span data-ttu-id="4ed55-999">In einer MVC-Ansicht:</span><span class="sxs-lookup"><span data-stu-id="4ed55-999">In an MVC view:</span></span>

```cshtml
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<!DOCTYPE html>
<html lang="en">
<head>
    <title>Index View</title>
</head>
<body>
    <h1>Access configuration in an MVC view</h1>
    <p>Configuration value for 'key': @Configuration["key"]</p>
</body>
</html>
```

## <a name="add-configuration-from-an-external-assembly"></a><span data-ttu-id="4ed55-1000">Hinzufügen von Konfigurationen aus einer externen Assembly</span><span class="sxs-lookup"><span data-stu-id="4ed55-1000">Add configuration from an external assembly</span></span>

<span data-ttu-id="4ed55-1001">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="4ed55-1001">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="4ed55-1002">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="4ed55-1002">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4ed55-1003">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4ed55-1003">Additional resources</span></span>

* <xref:fundamentals/configuration/options>

::: moniker-end
