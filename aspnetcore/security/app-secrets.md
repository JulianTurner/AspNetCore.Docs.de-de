---
title: Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie vertrauliche Informationen in Form von App-Geheimnissen während der Entwicklung einer ASP.net Core-APP speichern und abrufen.
ms.author: scaddie
ms.custom: mvc
ms.date: 4/20/2020
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
uid: security/app-secrets
ms.openlocfilehash: 174f831583c2ef6cb7f122a22fe855acc8fe3047
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056867"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="bd6ef-103">Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="bd6ef-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd6ef-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="bd6ef-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="bd6ef-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bd6ef-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bd6ef-106">Dieses Dokument erläutert Techniken zum Speichern und Abrufen von sensiblen Daten während der Entwicklung einer ASP.net Core-App auf einem Entwicklungs Computer.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-106">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="bd6ef-107">Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="bd6ef-108">Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="bd6ef-109">Geheimnisse sollten nicht mit der APP bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="bd6ef-110">Stattdessen sollten geheime Schlüssel in der Produktionsumgebung über eine kontrollierte Methode wie Umgebungsvariablen, Azure Key Vault usw. zur Verfügung gestellt werden. Sie können Azure-Test-und Produktionsgeheimnisse mit dem [Azure Key Vault Konfigurations Anbieters](xref:security/key-vault-configuration)speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-110">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="bd6ef-111">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="bd6ef-111">Environment variables</span></span>

<span data-ttu-id="bd6ef-112">Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-112">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="bd6ef-113">Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-113">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="bd6ef-114">Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-114">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="bd6ef-115">Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Projekt *:::no-loc(appsettings.json):::* Datei mit dem Schlüssel enthalten `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-115">A default database connection string is included in the project's *:::no-loc(appsettings.json):::* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="bd6ef-116">Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-116">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="bd6ef-117">Während der APP-Bereitstellung `DefaultConnection` kann der Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-117">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="bd6ef-118">In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-118">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="bd6ef-119">Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-119">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="bd6ef-120">Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-120">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="bd6ef-121">Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-121">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="bd6ef-122">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="bd6ef-122">Secret Manager</span></span>

<span data-ttu-id="bd6ef-123">Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-123">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="bd6ef-124">In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-124">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="bd6ef-125">App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-125">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="bd6ef-126">Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-126">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="bd6ef-127">Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt</span><span class="sxs-lookup"><span data-stu-id="bd6ef-127">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="bd6ef-128">Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-128">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="bd6ef-129">Dies ist nur für Entwicklungszwecke vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-129">It's for development purposes only.</span></span> <span data-ttu-id="bd6ef-130">Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="bd6ef-131">So funktioniert das Secret Manager-Tool</span><span class="sxs-lookup"><span data-stu-id="bd6ef-131">How the Secret Manager tool works</span></span>

<span data-ttu-id="bd6ef-132">Das Secret Manager-Tool abstrahiert die Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-132">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="bd6ef-133">Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-133">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="bd6ef-134">Die Werte werden in einer JSON-Konfigurationsdatei in einem vom System geschützten Benutzerprofil Ordner auf dem lokalen Computer gespeichert:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-134">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="bd6ef-135">Windows</span><span class="sxs-lookup"><span data-stu-id="bd6ef-135">Windows</span></span>](#tab/windows)

<span data-ttu-id="bd6ef-136">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-136">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="bd6ef-137">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="bd6ef-137">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="bd6ef-138">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-138">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="bd6ef-139">Ersetzen Sie in den vorangehenden Dateipfaden `<user_secrets_id>` durch den Wert, der `UserSecretsId` in der *csproj* -Datei angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-139">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="bd6ef-140">Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-140">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="bd6ef-141">Diese Implementierungsdetails können sich ändern.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-141">These implementation details may change.</span></span> <span data-ttu-id="bd6ef-142">Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-142">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="bd6ef-143">Aktivieren der geheimen Speicherung</span><span class="sxs-lookup"><span data-stu-id="bd6ef-143">Enable secret storage</span></span>

<span data-ttu-id="bd6ef-144">Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-144">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="bd6ef-145">Das Secret Manager-Tool enthält einen `init` Befehl in .net Core SDK 3.0.100 oder höher.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-145">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="bd6ef-146">Zum Verwenden von Benutzer Geheimnissen führen Sie den folgenden Befehl im Projektverzeichnis aus:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-146">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="bd6ef-147">Mit dem vorangehenden Befehl wird ein- `UserSecretsId` Element in einer `PropertyGroup` der *csproj* -Datei hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-147">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="bd6ef-148">Standardmäßig ist der innere Text von `UserSecretsId` eine GUID.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-148">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="bd6ef-149">Der innere Text ist willkürlich, aber für das Projekt eindeutig.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-149">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="bd6ef-150">Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-150">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="bd6ef-151">Diese Geste fügt `UserSecretsId` der *csproj* -Datei ein-Element, das mit einer GUID aufgefüllt ist, hinzu.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-151">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="bd6ef-152">Festlegen eines Geheimnisses</span><span class="sxs-lookup"><span data-stu-id="bd6ef-152">Set a secret</span></span>

<span data-ttu-id="bd6ef-153">Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-153">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="bd6ef-154">Das Geheimnis wird dem Wert des Projekts zugeordnet `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-154">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="bd6ef-155">Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-155">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="bd6ef-156">Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, dass `Movies` Ein Objektliteral mit einer- `ServiceApiKey` Eigenschaft ist.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-156">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="bd6ef-157">Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-157">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="bd6ef-158">Verwenden Sie die- `--project` Option, um den Dateisystempfad anzugeben, in dem die *csproj* -Datei vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-158">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="bd6ef-159">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-159">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="bd6ef-160">Vereinfachen der JSON-Struktur in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd6ef-160">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="bd6ef-161">Die Geste " **Benutzer Geheimnisse verwalten** " von Visual Studio öffnet eine *secrets.jsfür* die Datei im Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-161">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="bd6ef-162">Ersetzen Sie den Inhalt *secrets.jsauf* durch die zu speichernden Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-162">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="bd6ef-163">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-163">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="bd6ef-164">Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder vereinfacht `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-164">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="bd6ef-165">Beispielsweise wird durch `dotnet user-secrets remove "Movies:ConnectionString"` das Ausführen des `Movies` Objektliterals reduziert.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-165">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="bd6ef-166">Die geänderte Datei ähnelt der folgenden:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-166">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="bd6ef-167">Festlegen mehrerer geheimer Schlüssel</span><span class="sxs-lookup"><span data-stu-id="bd6ef-167">Set multiple secrets</span></span>

<span data-ttu-id="bd6ef-168">Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an den Befehl festgelegt werden `set` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-168">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="bd6ef-169">Im folgenden Beispiel werden die *input.jsfür* den Inhalt der Datei an den Befehl weitergeleitet `set` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-169">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="bd6ef-170">Windows</span><span class="sxs-lookup"><span data-stu-id="bd6ef-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="bd6ef-171">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-171">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="bd6ef-172">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="bd6ef-172">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="bd6ef-173">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-173">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="bd6ef-174">Auf einen geheimen Schlüssel zugreifen</span><span class="sxs-lookup"><span data-stu-id="bd6ef-174">Access a secret</span></span>

<span data-ttu-id="bd6ef-175">Die [ASP.net Core-Konfigurations-API](xref:fundamentals/configuration/index) ermöglicht den Zugriff auf Geheimnisse geheimer Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-175">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="bd6ef-176">Die Konfigurations Quelle für Benutzer Geheimnisse wird automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt aufruft <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> , um eine neue Instanz des Hosts mit vorkonfigurierten Standardwerten zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-176">The user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="bd6ef-177">`CreateDefaultBuilder` wird aufgerufen, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> wenn Folgendes <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> ist <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="bd6ef-177">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="bd6ef-178">Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse explizit hinzu, indem Sie aufrufen <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-178">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A>.</span></span> <span data-ttu-id="bd6ef-179">`AddUserSecrets`Wird nur aufgerufen, wenn die app in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-179">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Host&highlight=6-9)]

<span data-ttu-id="bd6ef-180">Benutzer Geheimnisse können über die API abgerufen werden `Configuration` :</span><span class="sxs-lookup"><span data-stu-id="bd6ef-180">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="bd6ef-181">Zuordnen von Geheimnissen zu einem poco</span><span class="sxs-lookup"><span data-stu-id="bd6ef-181">Map secrets to a POCO</span></span>

<span data-ttu-id="bd6ef-182">Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-182">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bd6ef-183">Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden Sie die `Configuration` [Objekt Diagramm-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Funktion der API.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-183">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="bd6ef-184">Der folgende Code bindet an ein benutzerdefiniertes `MovieSettings` poco und greift auf den- `ServiceApiKey` Eigenschafts Wert zu:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-184">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="bd6ef-185">Die `Movies:ConnectionString` `Movies:ServiceApiKey` Geheimnisse und werden den entsprechenden Eigenschaften in zugeordnet `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="bd6ef-185">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="bd6ef-186">Zeichen folgen Ersetzung mit geheimen Schlüsseln</span><span class="sxs-lookup"><span data-stu-id="bd6ef-186">String replacement with secrets</span></span>

<span data-ttu-id="bd6ef-187">Das Speichern von Kenn Wörtern im Klartext ist unsicher.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-187">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="bd6ef-188">Beispielsweise kann eine Datenbank-Verbindungs Zeichenfolge, die in gespeichert ist, *:::no-loc(appsettings.json):::* ein Kennwort für den angegebenen Benutzer enthalten:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-188">For example, a database connection string stored in *:::no-loc(appsettings.json):::* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="bd6ef-189">Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-189">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="bd6ef-190">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-190">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="bd6ef-191">Entfernen Sie das `Password` Schlüssel-Wert-Paar aus der Verbindungs Zeichenfolge in *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-191">Remove the `Password` key-value pair from the connection string in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="bd6ef-192">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-192">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/:::no-loc(appsettings.json):::?highlight=3)]

<span data-ttu-id="bd6ef-193">Der Wert des Geheimnisses kann für die-Eigenschaft eines-Objekts festgelegt werden <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> , um die Verbindungs Zeichenfolge abzuschließen:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-193">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="bd6ef-194">Auflisten der geheimen Schlüssel</span><span class="sxs-lookup"><span data-stu-id="bd6ef-194">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bd6ef-195">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-195">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="bd6ef-196">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-196">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="bd6ef-197">Im vorherigen Beispiel gibt ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in *secrets.jsan* .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-197">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="bd6ef-198">Entfernen eines einzelnen geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="bd6ef-198">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bd6ef-199">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-199">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="bd6ef-200">Die *secrets.jsfür* die Datei der App wurde geändert, um das Schlüssel-Wert-Paar zu entfernen, das dem Schlüssel zugeordnet ist `MoviesConnectionString` :</span><span class="sxs-lookup"><span data-stu-id="bd6ef-200">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="bd6ef-201">`dotnet user-secrets list` zeigt die folgende Meldung an:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-201">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="bd6ef-202">Alle geheimen Schlüssel entfernen</span><span class="sxs-lookup"><span data-stu-id="bd6ef-202">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bd6ef-203">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-203">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="bd6ef-204">Alle geheimen Benutzerschlüssel für die APP wurden aus dem *secrets.js* für die Datei gelöscht:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-204">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="bd6ef-205">Wird ausgeführt, wird `dotnet user-secrets list` die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-205">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="bd6ef-206">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bd6ef-206">Additional resources</span></span>

* <span data-ttu-id="bd6ef-207">Informationen zum Zugriff auf den geheimen Hauptschlüssel von IIS finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-207">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd6ef-208">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="bd6ef-208">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="bd6ef-209">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bd6ef-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="bd6ef-210">Dieses Dokument erläutert Techniken zum Speichern und Abrufen von sensiblen Daten während der Entwicklung einer ASP.net Core-App auf einem Entwicklungs Computer.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-210">This document explains techniques for storing and retrieving sensitive data during development of an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="bd6ef-211">Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-211">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="bd6ef-212">Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-212">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="bd6ef-213">Geheimnisse sollten nicht mit der APP bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-213">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="bd6ef-214">Stattdessen sollten geheime Schlüssel in der Produktionsumgebung über eine kontrollierte Methode wie Umgebungsvariablen, Azure Key Vault usw. zur Verfügung gestellt werden. Sie können Azure-Test-und Produktionsgeheimnisse mit dem [Azure Key Vault Konfigurations Anbieters](xref:security/key-vault-configuration)speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-214">Instead, secrets should be made available in the production environment through a controlled means like environment variables, Azure Key Vault, etc. You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="bd6ef-215">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="bd6ef-215">Environment variables</span></span>

<span data-ttu-id="bd6ef-216">Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-216">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="bd6ef-217">Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-217">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="bd6ef-218">Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-218">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="bd6ef-219">Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Projekt *:::no-loc(appsettings.json):::* Datei mit dem Schlüssel enthalten `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-219">A default database connection string is included in the project's *:::no-loc(appsettings.json):::* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="bd6ef-220">Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-220">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="bd6ef-221">Während der APP-Bereitstellung `DefaultConnection` kann der Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-221">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="bd6ef-222">In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-222">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="bd6ef-223">Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-223">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="bd6ef-224">Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-224">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="bd6ef-225">Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-225">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="bd6ef-226">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="bd6ef-226">Secret Manager</span></span>

<span data-ttu-id="bd6ef-227">Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-227">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="bd6ef-228">In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-228">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="bd6ef-229">App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-229">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="bd6ef-230">Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-230">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="bd6ef-231">Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt</span><span class="sxs-lookup"><span data-stu-id="bd6ef-231">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="bd6ef-232">Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-232">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="bd6ef-233">Dies ist nur für Entwicklungszwecke vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-233">It's for development purposes only.</span></span> <span data-ttu-id="bd6ef-234">Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-234">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="bd6ef-235">So funktioniert das Secret Manager-Tool</span><span class="sxs-lookup"><span data-stu-id="bd6ef-235">How the Secret Manager tool works</span></span>

<span data-ttu-id="bd6ef-236">Das Secret Manager-Tool abstrahiert die Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-236">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="bd6ef-237">Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-237">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="bd6ef-238">Die Werte werden in einer JSON-Konfigurationsdatei in einem vom System geschützten Benutzerprofil Ordner auf dem lokalen Computer gespeichert:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-238">The values are stored in a JSON configuration file in a system-protected user profile folder on the local machine:</span></span>

# <a name="windows"></a>[<span data-ttu-id="bd6ef-239">Windows</span><span class="sxs-lookup"><span data-stu-id="bd6ef-239">Windows</span></span>](#tab/windows)

<span data-ttu-id="bd6ef-240">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-240">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="bd6ef-241">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="bd6ef-241">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="bd6ef-242">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-242">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="bd6ef-243">Ersetzen Sie in den vorangehenden Dateipfaden `<user_secrets_id>` durch den Wert, der `UserSecretsId` in der *csproj* -Datei angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-243">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the *.csproj* file.</span></span>

<span data-ttu-id="bd6ef-244">Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-244">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="bd6ef-245">Diese Implementierungsdetails können sich ändern.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-245">These implementation details may change.</span></span> <span data-ttu-id="bd6ef-246">Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-246">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="bd6ef-247">Aktivieren der geheimen Speicherung</span><span class="sxs-lookup"><span data-stu-id="bd6ef-247">Enable secret storage</span></span>

<span data-ttu-id="bd6ef-248">Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-248">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="bd6ef-249">Definieren Sie ein- `UserSecretsId` Element in einer-Datei der `PropertyGroup` *csproj* -Datei, um Benutzer Geheimnisse zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-249">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="bd6ef-250">Der innere Text von `UserSecretsId` ist willkürlich, aber für das Projekt eindeutig.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-250">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="bd6ef-251">Entwickler generieren in der Regel eine GUID für das `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-251">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="bd6ef-252">Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-252">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="bd6ef-253">Diese Geste fügt `UserSecretsId` der *csproj* -Datei ein-Element, das mit einer GUID aufgefüllt ist, hinzu.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-253">This gesture adds a `UserSecretsId` element, populated with a GUID, to the *.csproj* file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="bd6ef-254">Festlegen eines Geheimnisses</span><span class="sxs-lookup"><span data-stu-id="bd6ef-254">Set a secret</span></span>

<span data-ttu-id="bd6ef-255">Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-255">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="bd6ef-256">Das Geheimnis wird dem Wert des Projekts zugeordnet `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-256">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="bd6ef-257">Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-257">For example, run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="bd6ef-258">Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, dass `Movies` Ein Objektliteral mit einer- `ServiceApiKey` Eigenschaft ist.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-258">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="bd6ef-259">Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-259">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="bd6ef-260">Verwenden Sie die- `--project` Option, um den Dateisystempfad anzugeben, in dem die *csproj* -Datei vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-260">Use the `--project` option to supply the file system path at which the *.csproj* file exists.</span></span> <span data-ttu-id="bd6ef-261">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-261">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="bd6ef-262">Vereinfachen der JSON-Struktur in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd6ef-262">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="bd6ef-263">Die Geste " **Benutzer Geheimnisse verwalten** " von Visual Studio öffnet eine *secrets.jsfür* die Datei im Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-263">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="bd6ef-264">Ersetzen Sie den Inhalt *secrets.jsauf* durch die zu speichernden Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-264">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="bd6ef-265">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-265">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="bd6ef-266">Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder vereinfacht `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-266">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="bd6ef-267">Beispielsweise wird durch `dotnet user-secrets remove "Movies:ConnectionString"` das Ausführen des `Movies` Objektliterals reduziert.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-267">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="bd6ef-268">Die geänderte Datei ähnelt der folgenden:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-268">The modified file resembles the following:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="bd6ef-269">Festlegen mehrerer geheimer Schlüssel</span><span class="sxs-lookup"><span data-stu-id="bd6ef-269">Set multiple secrets</span></span>

<span data-ttu-id="bd6ef-270">Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an den Befehl festgelegt werden `set` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-270">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="bd6ef-271">Im folgenden Beispiel werden die *input.jsfür* den Inhalt der Datei an den Befehl weitergeleitet `set` .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-271">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="bd6ef-272">Windows</span><span class="sxs-lookup"><span data-stu-id="bd6ef-272">Windows</span></span>](#tab/windows)

<span data-ttu-id="bd6ef-273">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-273">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="bd6ef-274">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="bd6ef-274">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="bd6ef-275">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-275">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="bd6ef-276">Auf einen geheimen Schlüssel zugreifen</span><span class="sxs-lookup"><span data-stu-id="bd6ef-276">Access a secret</span></span>

<span data-ttu-id="bd6ef-277">Die [ASP.net Core-Konfigurations-API](xref:fundamentals/configuration/index) ermöglicht den Zugriff auf Geheimnisse geheimer Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-277">The [ASP.NET Core Configuration API](xref:fundamentals/configuration/index) provides access to Secret Manager secrets.</span></span>

<span data-ttu-id="bd6ef-278">Wenn das Projekt .NET Framework als Ziel hat, installieren Sie die [Microsoft.Extensions.Config-urung. Usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) -nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-278">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="bd6ef-279">In ASP.net Core 2,0 oder höher wird die Konfigurations Quelle "Benutzer Geheimnisse" automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt aufruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> , um eine neue Instanz des Hosts mit vorkonfigurierten Standardwerten zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-279">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> to initialize a new instance of the host with preconfigured defaults.</span></span> <span data-ttu-id="bd6ef-280">`CreateDefaultBuilder` wird aufgerufen, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> wenn Folgendes <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> ist <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="bd6ef-280">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="bd6ef-281">Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse explizit hinzu, indem Sie <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> im- `Startup` Konstruktor aufrufen.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-281">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="bd6ef-282">`AddUserSecrets`Wird nur aufgerufen, wenn die app in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-282">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="bd6ef-283">Benutzer Geheimnisse können über die API abgerufen werden `Configuration` :</span><span class="sxs-lookup"><span data-stu-id="bd6ef-283">User secrets can be retrieved via the `Configuration` API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="bd6ef-284">Zuordnen von Geheimnissen zu einem poco</span><span class="sxs-lookup"><span data-stu-id="bd6ef-284">Map secrets to a POCO</span></span>

<span data-ttu-id="bd6ef-285">Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-285">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bd6ef-286">Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden Sie die `Configuration` [Objekt Diagramm-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Funktion der API.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-286">To map the preceding secrets to a POCO, use the `Configuration` API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="bd6ef-287">Der folgende Code bindet an ein benutzerdefiniertes `MovieSettings` poco und greift auf den- `ServiceApiKey` Eigenschafts Wert zu:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-287">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="bd6ef-288">Die `Movies:ConnectionString` `Movies:ServiceApiKey` Geheimnisse und werden den entsprechenden Eigenschaften in zugeordnet `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="bd6ef-288">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="bd6ef-289">Zeichen folgen Ersetzung mit geheimen Schlüsseln</span><span class="sxs-lookup"><span data-stu-id="bd6ef-289">String replacement with secrets</span></span>

<span data-ttu-id="bd6ef-290">Das Speichern von Kenn Wörtern im Klartext ist unsicher.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-290">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="bd6ef-291">Beispielsweise kann eine Datenbank-Verbindungs Zeichenfolge, die in gespeichert ist, *:::no-loc(appsettings.json):::* ein Kennwort für den angegebenen Benutzer enthalten:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-291">For example, a database connection string stored in *:::no-loc(appsettings.json):::* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="bd6ef-292">Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern.</span><span class="sxs-lookup"><span data-stu-id="bd6ef-292">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="bd6ef-293">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-293">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="bd6ef-294">Entfernen Sie das `Password` Schlüssel-Wert-Paar aus der Verbindungs Zeichenfolge in *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-294">Remove the `Password` key-value pair from the connection string in *:::no-loc(appsettings.json):::* .</span></span> <span data-ttu-id="bd6ef-295">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-295">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/:::no-loc(appsettings.json):::?highlight=3)]

<span data-ttu-id="bd6ef-296">Der Wert des Geheimnisses kann für die-Eigenschaft eines-Objekts festgelegt werden <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> , um die Verbindungs Zeichenfolge abzuschließen:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-296">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="bd6ef-297">Auflisten der geheimen Schlüssel</span><span class="sxs-lookup"><span data-stu-id="bd6ef-297">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bd6ef-298">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-298">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="bd6ef-299">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-299">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="bd6ef-300">Im vorherigen Beispiel gibt ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in *secrets.jsan* .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-300">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json* .</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="bd6ef-301">Entfernen eines einzelnen geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="bd6ef-301">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bd6ef-302">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-302">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="bd6ef-303">Die *secrets.jsfür* die Datei der App wurde geändert, um das Schlüssel-Wert-Paar zu entfernen, das dem Schlüssel zugeordnet ist `MoviesConnectionString` :</span><span class="sxs-lookup"><span data-stu-id="bd6ef-303">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="bd6ef-304">Wird ausgeführt, wird `dotnet user-secrets list` die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-304">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="bd6ef-305">Alle geheimen Schlüssel entfernen</span><span class="sxs-lookup"><span data-stu-id="bd6ef-305">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="bd6ef-306">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die *csproj* -Datei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-306">Run the following command from the directory in which the *.csproj* file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="bd6ef-307">Alle geheimen Benutzerschlüssel für die APP wurden aus dem *secrets.js* für die Datei gelöscht:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-307">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="bd6ef-308">Wird ausgeführt, wird `dotnet user-secrets list` die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="bd6ef-308">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="bd6ef-309">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bd6ef-309">Additional resources</span></span>

* <span data-ttu-id="bd6ef-310">Informationen zum Zugriff auf den geheimen Hauptschlüssel von IIS finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="bd6ef-310">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing Secret Manager from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end