---
title: Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie vertrauliche Informationen während der Entwicklung einer ASP.net Core-APP speichern und abrufen.
ms.author: scaddie
ms.custom: mvc, contperfq2
ms.date: 11/24/2020
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
uid: security/app-secrets
ms.openlocfilehash: 99b7b04076206f95c04da79283010beafdd1cc88
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035852"
---
# <a name="safe-storage-of-app-secrets-in-development-in-aspnet-core"></a><span data-ttu-id="86ad7-103">Sichere Speicherung von App-Geheimnissen in der Entwicklung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="86ad7-103">Safe storage of app secrets in development in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="86ad7-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="86ad7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="86ad7-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="86ad7-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="86ad7-106">In diesem Dokument wird erläutert, wie sensible Daten für eine ASP.net Core-App auf einem Entwicklungs Computer verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-106">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="86ad7-107">Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode.</span><span class="sxs-lookup"><span data-stu-id="86ad7-107">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="86ad7-108">Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-108">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="86ad7-109">Geheimnisse sollten nicht mit der APP bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-109">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="86ad7-110">Stattdessen sollte auf die Produktionsgeheimnisse über ein kontrolliertes Mittel wie Umgebungsvariablen oder Azure Key Vault zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-110">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="86ad7-111">Sie können Azure-Test- und -Produktionsgeheimnisse mit dem [Konfigurationsanbieter Azure Key Vault](xref:security/key-vault-configuration) speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-111">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="86ad7-112">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="86ad7-112">Environment variables</span></span>

<span data-ttu-id="86ad7-113">Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-113">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="86ad7-114">Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-114">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="86ad7-115">Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="86ad7-115">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="86ad7-116">Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Projekt *appsettings.json* Datei mit dem Schlüssel enthalten `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-116">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="86ad7-117">Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-117">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="86ad7-118">Während der APP-Bereitstellung `DefaultConnection` kann der Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-118">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="86ad7-119">In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-119">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="86ad7-120">Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-120">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="86ad7-121">Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-121">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="86ad7-122">Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="86ad7-122">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="86ad7-123">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="86ad7-123">Secret Manager</span></span>

<span data-ttu-id="86ad7-124">Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="86ad7-124">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="86ad7-125">In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="86ad7-125">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="86ad7-126">App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-126">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="86ad7-127">Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-127">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="86ad7-128">Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt</span><span class="sxs-lookup"><span data-stu-id="86ad7-128">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="86ad7-129">Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-129">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="86ad7-130">Dies ist nur für Entwicklungszwecke vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-130">It's for development purposes only.</span></span> <span data-ttu-id="86ad7-131">Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-131">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="86ad7-132">So funktioniert das Secret Manager-Tool</span><span class="sxs-lookup"><span data-stu-id="86ad7-132">How the Secret Manager tool works</span></span>

<span data-ttu-id="86ad7-133">Das Secret Manager-Tool verbirgt Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-133">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="86ad7-134">Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-134">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="86ad7-135">Die Werte werden in einer JSON-Datei im Benutzerprofil Ordner des lokalen Computers gespeichert:</span><span class="sxs-lookup"><span data-stu-id="86ad7-135">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="86ad7-136">Windows</span><span class="sxs-lookup"><span data-stu-id="86ad7-136">Windows</span></span>](#tab/windows)

<span data-ttu-id="86ad7-137">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="86ad7-137">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="86ad7-138">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="86ad7-138">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="86ad7-139">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="86ad7-139">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="86ad7-140">Ersetzen Sie in den vorangehenden Dateipfaden `<user_secrets_id>` durch den `UserSecretsId` in der Projektdatei angegebenen Wert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-140">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="86ad7-141">Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt.</span><span class="sxs-lookup"><span data-stu-id="86ad7-141">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="86ad7-142">Diese Implementierungsdetails können sich ändern.</span><span class="sxs-lookup"><span data-stu-id="86ad7-142">These implementation details may change.</span></span> <span data-ttu-id="86ad7-143">Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-143">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="86ad7-144">Aktivieren der geheimen Speicherung</span><span class="sxs-lookup"><span data-stu-id="86ad7-144">Enable secret storage</span></span>

<span data-ttu-id="86ad7-145">Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="86ad7-145">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="86ad7-146">Das Secret Manager-Tool enthält einen `init` Befehl in .net Core SDK 3.0.100 oder höher.</span><span class="sxs-lookup"><span data-stu-id="86ad7-146">The Secret Manager tool includes an `init` command in .NET Core SDK 3.0.100 or later.</span></span> <span data-ttu-id="86ad7-147">Zum Verwenden von Benutzer Geheimnissen führen Sie den folgenden Befehl im Projektverzeichnis aus:</span><span class="sxs-lookup"><span data-stu-id="86ad7-147">To use user secrets, run the following command in the project directory:</span></span>

```dotnetcli
dotnet user-secrets init
```

<span data-ttu-id="86ad7-148">Mit dem vorangehenden Befehl wird ein- `UserSecretsId` Element in einer `PropertyGroup` der Projektdatei hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="86ad7-148">The preceding command adds a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="86ad7-149">Standardmäßig ist der innere Text von `UserSecretsId` eine GUID.</span><span class="sxs-lookup"><span data-stu-id="86ad7-149">By default, the inner text of `UserSecretsId` is a GUID.</span></span> <span data-ttu-id="86ad7-150">Der innere Text ist willkürlich, aber für das Projekt eindeutig.</span><span class="sxs-lookup"><span data-stu-id="86ad7-150">The inner text is arbitrary, but is unique to the project.</span></span>

[!code-xml[](app-secrets/samples/3.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

<span data-ttu-id="86ad7-151">Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="86ad7-151">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="86ad7-152">Diese Geste fügt `UserSecretsId` der Projektdatei ein-Element, das mit einer GUID aufgefüllt ist, hinzu.</span><span class="sxs-lookup"><span data-stu-id="86ad7-152">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="86ad7-153">Festlegen eines Geheimnisses</span><span class="sxs-lookup"><span data-stu-id="86ad7-153">Set a secret</span></span>

<span data-ttu-id="86ad7-154">Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht.</span><span class="sxs-lookup"><span data-stu-id="86ad7-154">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="86ad7-155">Das Geheimnis wird dem Wert des Projekts zugeordnet `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-155">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="86ad7-156">Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="86ad7-156">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="86ad7-157">Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, dass `Movies` Ein Objektliteral mit einer- `ServiceApiKey` Eigenschaft ist.</span><span class="sxs-lookup"><span data-stu-id="86ad7-157">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="86ad7-158">Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-158">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="86ad7-159">Verwenden Sie die- `--project` Option, um den Dateisystempfad anzugeben, in dem die Projektdatei vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="86ad7-159">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="86ad7-160">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="86ad7-160">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="86ad7-161">Vereinfachen der JSON-Struktur in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86ad7-161">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="86ad7-162">Die Geste " **Benutzer Geheimnisse verwalten** " von Visual Studio öffnet eine *secrets.jsfür* die Datei im Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="86ad7-162">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="86ad7-163">Ersetzen Sie den Inhalt *secrets.jsauf* durch die zu speichernden Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="86ad7-163">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="86ad7-164">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="86ad7-164">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="86ad7-165">Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder vereinfacht `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-165">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="86ad7-166">Beispielsweise wird durch `dotnet user-secrets remove "Movies:ConnectionString"` das Ausführen des `Movies` Objektliterals reduziert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-166">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="86ad7-167">Die geänderte Datei ähnelt der folgenden JSON-Datei:</span><span class="sxs-lookup"><span data-stu-id="86ad7-167">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="86ad7-168">Festlegen mehrerer geheimer Schlüssel</span><span class="sxs-lookup"><span data-stu-id="86ad7-168">Set multiple secrets</span></span>

<span data-ttu-id="86ad7-169">Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an den Befehl festgelegt werden `set` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-169">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="86ad7-170">Im folgenden Beispiel werden die *input.jsfür* den Inhalt der Datei an den Befehl weitergeleitet `set` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-170">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="86ad7-171">Windows</span><span class="sxs-lookup"><span data-stu-id="86ad7-171">Windows</span></span>](#tab/windows)

<span data-ttu-id="86ad7-172">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="86ad7-172">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="86ad7-173">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="86ad7-173">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="86ad7-174">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="86ad7-174">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="86ad7-175">Auf einen geheimen Schlüssel zugreifen</span><span class="sxs-lookup"><span data-stu-id="86ad7-175">Access a secret</span></span>

<span data-ttu-id="86ad7-176">Um auf ein Geheimnis zuzugreifen, führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="86ad7-176">To access a secret, complete the following steps:</span></span>

1. [<span data-ttu-id="86ad7-177">Registrieren der Konfigurations Quelle für Benutzer Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="86ad7-177">Register the user secrets configuration source</span></span>](#register-the-user-secrets-configuration-source)
1. [<span data-ttu-id="86ad7-178">Lesen des geheimen Schlüssels über die Konfigurations-API</span><span class="sxs-lookup"><span data-stu-id="86ad7-178">Read the secret via the Configuration API</span></span>](#read-the-secret-via-the-configuration-api)

### <a name="register-the-user-secrets-configuration-source"></a><span data-ttu-id="86ad7-179">Registrieren der Konfigurations Quelle für Benutzer Geheimnisse</span><span class="sxs-lookup"><span data-stu-id="86ad7-179">Register the user secrets configuration source</span></span>

<span data-ttu-id="86ad7-180">Der [Konfigurations Anbieter](/dotnet/core/extensions/configuration-providers) für Benutzer Geheimnisse registriert die geeignete Konfigurations Quelle bei der .net- [Konfigurations-API](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="86ad7-180">The user secrets [configuration provider](/dotnet/core/extensions/configuration-providers) registers the appropriate configuration source with the .NET [Configuration API](xref:fundamentals/configuration/index).</span></span>

<span data-ttu-id="86ad7-181">Die Konfigurations Quelle für Benutzer Geheimnisse wird automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt aufruft <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="86ad7-181">The user secrets configuration source is automatically added in Development mode when the project calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="86ad7-182">`CreateDefaultBuilder` wird aufgerufen, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> wenn Folgendes <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> ist <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="86ad7-182">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName> is <xref:Microsoft.Extensions.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program.cs?name=snippet_CreateHostBuilder&highlight=2)]

<span data-ttu-id="86ad7-183">Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse explizit hinzu, indem Sie <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in Aufrufen <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A> .</span><span class="sxs-lookup"><span data-stu-id="86ad7-183">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration%2A>.</span></span> <span data-ttu-id="86ad7-184">`AddUserSecrets`Wird nur aufgerufen, wenn die app in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="86ad7-184">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Program2.cs?name=snippet_Program&highlight=10-13)]

### <a name="read-the-secret-via-the-configuration-api"></a><span data-ttu-id="86ad7-185">Lesen des geheimen Schlüssels über die Konfigurations-API</span><span class="sxs-lookup"><span data-stu-id="86ad7-185">Read the secret via the Configuration API</span></span>

<span data-ttu-id="86ad7-186">Wenn die Konfigurations Quelle "Benutzer Geheimnisse" registriert ist, kann die .NET-Konfigurations-API die geheimen Schlüssel lesen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-186">If the user secrets configuration source is registered, the .NET Configuration API can read the secrets.</span></span> <span data-ttu-id="86ad7-187">Die [Konstruktorinjektion](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) kann verwendet werden, um Zugriff auf die .NET-Konfigurations-API zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="86ad7-187">[Constructor injection](/dotnet/core/extensions/dependency-injection#constructor-injection-behavior) can be used to gain access to the .NET Configuration API.</span></span> <span data-ttu-id="86ad7-188">Beachten Sie die folgenden Beispiele, um den Schlüssel zu lesen `Movies:ServiceApiKey` :</span><span class="sxs-lookup"><span data-stu-id="86ad7-188">Consider the following examples of reading the `Movies:ServiceApiKey` key:</span></span>

<span data-ttu-id="86ad7-189">**Startup-Klasse:**</span><span class="sxs-lookup"><span data-stu-id="86ad7-189">**Startup class:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

<span data-ttu-id="86ad7-190">**Razor Seiten Modell der Seite:**</span><span class="sxs-lookup"><span data-stu-id="86ad7-190">**Razor Pages page model:**</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Pages/Index.cshtml.cs?name=snippet_PageModel&highlight=12)]

<span data-ttu-id="86ad7-191">Weitere Informationen finden Sie unter [Zugriffs Konfiguration in Start](xref:fundamentals/configuration/index#access-configuration-in-startup) -und [Zugriffs Konfiguration in Razor Seiten](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span><span class="sxs-lookup"><span data-stu-id="86ad7-191">For more information, see [Access configuration in Startup](xref:fundamentals/configuration/index#access-configuration-in-startup) and [Access configuration in Razor Pages](xref:fundamentals/configuration/index#access-configuration-in-razor-pages).</span></span>

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="86ad7-192">Zuordnen von Geheimnissen zu einem poco</span><span class="sxs-lookup"><span data-stu-id="86ad7-192">Map secrets to a POCO</span></span>

<span data-ttu-id="86ad7-193">Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="86ad7-193">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="86ad7-194">Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden Sie die [Objekt Diagramm-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Funktion der .NET-Konfigurations-API.</span><span class="sxs-lookup"><span data-stu-id="86ad7-194">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="86ad7-195">Der folgende Code bindet an ein benutzerdefiniertes `MovieSettings` poco und greift auf den- `ServiceApiKey` Eigenschafts Wert zu:</span><span class="sxs-lookup"><span data-stu-id="86ad7-195">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="86ad7-196">Die `Movies:ConnectionString` `Movies:ServiceApiKey` Geheimnisse und werden den entsprechenden Eigenschaften in zugeordnet `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="86ad7-196">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="86ad7-197">Zeichen folgen Ersetzung mit geheimen Schlüsseln</span><span class="sxs-lookup"><span data-stu-id="86ad7-197">String replacement with secrets</span></span>

<span data-ttu-id="86ad7-198">Das Speichern von Kenn Wörtern im Klartext ist unsicher.</span><span class="sxs-lookup"><span data-stu-id="86ad7-198">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="86ad7-199">Beispielsweise kann eine Datenbank-Verbindungs Zeichenfolge, die in gespeichert ist, *appsettings.json* ein Kennwort für den angegebenen Benutzer enthalten:</span><span class="sxs-lookup"><span data-stu-id="86ad7-199">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="86ad7-200">Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern.</span><span class="sxs-lookup"><span data-stu-id="86ad7-200">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="86ad7-201">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="86ad7-201">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="86ad7-202">Entfernen Sie das `Password` Schlüssel-Wert-Paar aus der Verbindungs Zeichenfolge in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="86ad7-202">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="86ad7-203">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="86ad7-203">For example:</span></span>

[!code-json[](app-secrets/samples/3.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="86ad7-204">Der Wert des Geheimnisses kann für die-Eigenschaft eines-Objekts festgelegt werden <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> , um die Verbindungs Zeichenfolge abzuschließen:</span><span class="sxs-lookup"><span data-stu-id="86ad7-204">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/3.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="86ad7-205">Auflisten der geheimen Schlüssel</span><span class="sxs-lookup"><span data-stu-id="86ad7-205">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="86ad7-206">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="86ad7-206">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="86ad7-207">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="86ad7-207">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="86ad7-208">Im vorherigen Beispiel gibt ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in *secrets.jsan*.</span><span class="sxs-lookup"><span data-stu-id="86ad7-208">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="86ad7-209">Entfernen eines einzelnen geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="86ad7-209">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="86ad7-210">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="86ad7-210">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="86ad7-211">Die *secrets.jsfür* die Datei der App wurde geändert, um das Schlüssel-Wert-Paar zu entfernen, das dem Schlüssel zugeordnet ist `MoviesConnectionString` :</span><span class="sxs-lookup"><span data-stu-id="86ad7-211">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="86ad7-212">`dotnet user-secrets list` zeigt die folgende Meldung an:</span><span class="sxs-lookup"><span data-stu-id="86ad7-212">`dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="86ad7-213">Alle geheimen Schlüssel entfernen</span><span class="sxs-lookup"><span data-stu-id="86ad7-213">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="86ad7-214">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="86ad7-214">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="86ad7-215">Alle geheimen Benutzerschlüssel für die APP wurden aus dem *secrets.js* für die Datei gelöscht:</span><span class="sxs-lookup"><span data-stu-id="86ad7-215">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="86ad7-216">Wird ausgeführt, wird `dotnet user-secrets list` die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="86ad7-216">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="86ad7-217">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="86ad7-217">Additional resources</span></span>

* <span data-ttu-id="86ad7-218">Informationen zum Zugriff auf Benutzer Geheimnisse aus IIS finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="86ad7-218">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="86ad7-219">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27)und [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="86ad7-219">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="86ad7-220">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="86ad7-220">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/app-secrets/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="86ad7-221">In diesem Dokument wird erläutert, wie sensible Daten für eine ASP.net Core-App auf einem Entwicklungs Computer verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-221">This document explains how to manage sensitive data for an ASP.NET Core app on a development machine.</span></span> <span data-ttu-id="86ad7-222">Speichern Sie niemals Kenn Wörter oder andere sensible Daten im Quellcode.</span><span class="sxs-lookup"><span data-stu-id="86ad7-222">Never store passwords or other sensitive data in source code.</span></span> <span data-ttu-id="86ad7-223">Produktionsgeheimnisse sollten nicht für Entwicklungs-oder Testzwecke verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-223">Production secrets shouldn't be used for development or test.</span></span> <span data-ttu-id="86ad7-224">Geheimnisse sollten nicht mit der APP bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-224">Secrets shouldn't be deployed with the app.</span></span> <span data-ttu-id="86ad7-225">Stattdessen sollte auf die Produktionsgeheimnisse über ein kontrolliertes Mittel wie Umgebungsvariablen oder Azure Key Vault zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-225">Instead, production secrets should be accessed through a controlled means like environment variables or Azure Key Vault.</span></span> <span data-ttu-id="86ad7-226">Sie können Azure-Test- und -Produktionsgeheimnisse mit dem [Konfigurationsanbieter Azure Key Vault](xref:security/key-vault-configuration) speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-226">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

## <a name="environment-variables"></a><span data-ttu-id="86ad7-227">Umgebungsvariablen</span><span class="sxs-lookup"><span data-stu-id="86ad7-227">Environment variables</span></span>

<span data-ttu-id="86ad7-228">Umgebungsvariablen werden verwendet, um das Speichern von App-Geheimnissen im Code oder in lokalen Konfigurationsdateien zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-228">Environment variables are used to avoid storage of app secrets in code or in local configuration files.</span></span> <span data-ttu-id="86ad7-229">Umgebungsvariablen überschreiben Konfigurationswerte für alle zuvor angegebenen Konfigurations Quellen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-229">Environment variables override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="86ad7-230">Stellen Sie sich eine ASP.net Core Web-App vor, in der die Sicherheit **einzelner Benutzerkonten** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="86ad7-230">Consider an ASP.NET Core web app in which **Individual User Accounts** security is enabled.</span></span> <span data-ttu-id="86ad7-231">Eine standardmäßige Daten bankverbindungs Zeichenfolge ist in der Projekt *appsettings.json* Datei mit dem Schlüssel enthalten `DefaultConnection` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-231">A default database connection string is included in the project's *appsettings.json* file with the key `DefaultConnection`.</span></span> <span data-ttu-id="86ad7-232">Die Standard Verbindungs Zeichenfolge ist für localdb, das im Benutzermodus ausgeführt wird und kein Kennwort erfordert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-232">The default connection string is for LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="86ad7-233">Während der APP-Bereitstellung `DefaultConnection` kann der Schlüsselwert mit dem Wert einer Umgebungsvariablen überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-233">During app deployment, the `DefaultConnection` key value can be overridden with an environment variable's value.</span></span> <span data-ttu-id="86ad7-234">In der Umgebungsvariablen kann die gesamte Verbindungs Zeichenfolge mit vertraulichen Anmelde Informationen gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-234">The environment variable may store the complete connection string with sensitive credentials.</span></span>

> [!WARNING]
> <span data-ttu-id="86ad7-235">Umgebungsvariablen werden im Allgemeinen in unverschlüsseltem, unverschlüsseltem Text gespeichert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-235">Environment variables are generally stored in plain, unencrypted text.</span></span> <span data-ttu-id="86ad7-236">Wenn der Computer oder der Prozess kompromittiert ist, kann von nicht vertrauenswürdigen Parteien auf Umgebungsvariablen zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-236">If the machine or process is compromised, environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="86ad7-237">Möglicherweise sind zusätzliche Maßnahmen erforderlich, um eine Offenlegung von Benutzer Geheimnissen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="86ad7-237">Additional measures to prevent disclosure of user secrets may be required.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="secret-manager"></a><span data-ttu-id="86ad7-238">Secret Manager</span><span class="sxs-lookup"><span data-stu-id="86ad7-238">Secret Manager</span></span>

<span data-ttu-id="86ad7-239">Das Secret Manager-Tool speichert vertrauliche Daten während der Entwicklung eines ASP.net Core Projekts.</span><span class="sxs-lookup"><span data-stu-id="86ad7-239">The Secret Manager tool stores sensitive data during the development of an ASP.NET Core project.</span></span> <span data-ttu-id="86ad7-240">In diesem Zusammenhang handelt es sich bei einem sensiblen Datenobjekt um einen geheimen App-Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="86ad7-240">In this context, a piece of sensitive data is an app secret.</span></span> <span data-ttu-id="86ad7-241">App-Geheimnisse werden an einem anderen Speicherort als in der Projektstruktur gespeichert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-241">App secrets are stored in a separate location from the project tree.</span></span> <span data-ttu-id="86ad7-242">Die geheimen App-Schlüssel sind einem bestimmten Projekt zugeordnet oder können für mehrere Projekte freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-242">The app secrets are associated with a specific project or shared across several projects.</span></span> <span data-ttu-id="86ad7-243">Die geheimen App-Schlüssel werden nicht in die Quell Code Verwaltung eingecheckt</span><span class="sxs-lookup"><span data-stu-id="86ad7-243">The app secrets aren't checked into source control.</span></span>

> [!WARNING]
> <span data-ttu-id="86ad7-244">Das Secret Manager-Tool verschlüsselt die gespeicherten geheimen Schlüssel nicht und sollte nicht als vertrauenswürdiger Speicher behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-244">The Secret Manager tool doesn't encrypt the stored secrets and shouldn't be treated as a trusted store.</span></span> <span data-ttu-id="86ad7-245">Dies ist nur für Entwicklungszwecke vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-245">It's for development purposes only.</span></span> <span data-ttu-id="86ad7-246">Die Schlüssel und Werte werden in einer JSON-Konfigurationsdatei im Benutzerprofil Verzeichnis gespeichert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-246">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="86ad7-247">So funktioniert das Secret Manager-Tool</span><span class="sxs-lookup"><span data-stu-id="86ad7-247">How the Secret Manager tool works</span></span>

<span data-ttu-id="86ad7-248">Das Secret Manager-Tool verbirgt Implementierungsdetails, z. b. wo und wie die Werte gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-248">The Secret Manager tool hides implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="86ad7-249">Sie können das Tool verwenden, ohne diese Implementierungsdetails kennen zu lernen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-249">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="86ad7-250">Die Werte werden in einer JSON-Datei im Benutzerprofil Ordner des lokalen Computers gespeichert:</span><span class="sxs-lookup"><span data-stu-id="86ad7-250">The values are stored in a JSON file in the local machine's user profile folder:</span></span>

# <a name="windows"></a>[<span data-ttu-id="86ad7-251">Windows</span><span class="sxs-lookup"><span data-stu-id="86ad7-251">Windows</span></span>](#tab/windows)

<span data-ttu-id="86ad7-252">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="86ad7-252">File system path:</span></span>

`%APPDATA%\Microsoft\UserSecrets\<user_secrets_id>\secrets.json`

# <a name="linux--macos"></a>[<span data-ttu-id="86ad7-253">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="86ad7-253">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="86ad7-254">Dateisystempfad:</span><span class="sxs-lookup"><span data-stu-id="86ad7-254">File system path:</span></span>

`~/.microsoft/usersecrets/<user_secrets_id>/secrets.json`

---

<span data-ttu-id="86ad7-255">Ersetzen Sie in den vorangehenden Dateipfaden `<user_secrets_id>` durch den `UserSecretsId` in der Projektdatei angegebenen Wert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-255">In the preceding file paths, replace `<user_secrets_id>` with the `UserSecretsId` value specified in the project file.</span></span>

<span data-ttu-id="86ad7-256">Schreiben Sie keinen Code, der vom Speicherort oder Format der mit dem Geheimnis-Manager-Tool gespeicherten Daten abhängt.</span><span class="sxs-lookup"><span data-stu-id="86ad7-256">Don't write code that depends on the location or format of data saved with the Secret Manager tool.</span></span> <span data-ttu-id="86ad7-257">Diese Implementierungsdetails können sich ändern.</span><span class="sxs-lookup"><span data-stu-id="86ad7-257">These implementation details may change.</span></span> <span data-ttu-id="86ad7-258">Die geheimen Werte sind z. b. nicht verschlüsselt, können aber in der Zukunft liegen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-258">For example, the secret values aren't encrypted, but could be in the future.</span></span>

## <a name="enable-secret-storage"></a><span data-ttu-id="86ad7-259">Aktivieren der geheimen Speicherung</span><span class="sxs-lookup"><span data-stu-id="86ad7-259">Enable secret storage</span></span>

<span data-ttu-id="86ad7-260">Das Secret Manager-Tool arbeitet mit projektspezifischen Konfigurationseinstellungen, die in Ihrem Benutzerprofil gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="86ad7-260">The Secret Manager tool operates on project-specific configuration settings stored in your user profile.</span></span>

<span data-ttu-id="86ad7-261">Zum Verwenden von Benutzer Geheimnissen definieren Sie ein- `UserSecretsId` Element innerhalb einer `PropertyGroup` der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="86ad7-261">To use user secrets, define a `UserSecretsId` element within a `PropertyGroup` of the project file.</span></span> <span data-ttu-id="86ad7-262">Der innere Text von `UserSecretsId` ist willkürlich, aber für das Projekt eindeutig.</span><span class="sxs-lookup"><span data-stu-id="86ad7-262">The inner text of `UserSecretsId` is arbitrary, but is unique to the project.</span></span> <span data-ttu-id="86ad7-263">Entwickler generieren in der Regel eine GUID für das `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-263">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

[!code-xml[](app-secrets/samples/2.x/UserSecrets/UserSecrets.csproj?name=snippet_PropertyGroup&highlight=3)]

> [!TIP]
> <span data-ttu-id="86ad7-264">Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt in Projektmappen-Explorer, und wählen Sie im Kontextmenü die Option **Benutzer Geheimnisse verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="86ad7-264">In Visual Studio, right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="86ad7-265">Diese Geste fügt `UserSecretsId` der Projektdatei ein-Element, das mit einer GUID aufgefüllt ist, hinzu.</span><span class="sxs-lookup"><span data-stu-id="86ad7-265">This gesture adds a `UserSecretsId` element, populated with a GUID, to the project file.</span></span>

## <a name="set-a-secret"></a><span data-ttu-id="86ad7-266">Festlegen eines Geheimnisses</span><span class="sxs-lookup"><span data-stu-id="86ad7-266">Set a secret</span></span>

<span data-ttu-id="86ad7-267">Definieren Sie einen geheimen App-Schlüssel, der aus einem Schlüssel und seinem Wert besteht.</span><span class="sxs-lookup"><span data-stu-id="86ad7-267">Define an app secret consisting of a key and its value.</span></span> <span data-ttu-id="86ad7-268">Das Geheimnis wird dem Wert des Projekts zugeordnet `UserSecretsId` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-268">The secret is associated with the project's `UserSecretsId` value.</span></span> <span data-ttu-id="86ad7-269">Führen Sie z. b. den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="86ad7-269">For example, run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345"
```

<span data-ttu-id="86ad7-270">Im vorherigen Beispiel deutet der Doppelpunkt darauf hin, dass `Movies` Ein Objektliteral mit einer- `ServiceApiKey` Eigenschaft ist.</span><span class="sxs-lookup"><span data-stu-id="86ad7-270">In the preceding example, the colon denotes that `Movies` is an object literal with a `ServiceApiKey` property.</span></span>

<span data-ttu-id="86ad7-271">Das Secret Manager-Tool kann auch aus anderen Verzeichnissen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="86ad7-271">The Secret Manager tool can be used from other directories too.</span></span> <span data-ttu-id="86ad7-272">Verwenden Sie die- `--project` Option, um den Dateisystempfad anzugeben, in dem die Projektdatei vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="86ad7-272">Use the `--project` option to supply the file system path at which the project file exists.</span></span> <span data-ttu-id="86ad7-273">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="86ad7-273">For example:</span></span>

```dotnetcli
dotnet user-secrets set "Movies:ServiceApiKey" "12345" --project "C:\apps\WebApp1\src\WebApp1"
```

### <a name="json-structure-flattening-in-visual-studio"></a><span data-ttu-id="86ad7-274">Vereinfachen der JSON-Struktur in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="86ad7-274">JSON structure flattening in Visual Studio</span></span>

<span data-ttu-id="86ad7-275">Die Geste " **Benutzer Geheimnisse verwalten** " von Visual Studio öffnet eine *secrets.jsfür* die Datei im Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="86ad7-275">Visual Studio's **Manage User Secrets** gesture opens a *secrets.json* file in the text editor.</span></span> <span data-ttu-id="86ad7-276">Ersetzen Sie den Inhalt *secrets.jsauf* durch die zu speichernden Schlüssel-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="86ad7-276">Replace the contents of *secrets.json* with the key-value pairs to be stored.</span></span> <span data-ttu-id="86ad7-277">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="86ad7-277">For example:</span></span>

```json
{
  "Movies": {
    "ConnectionString": "Server=(localdb)\\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true",
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="86ad7-278">Die JSON-Struktur wird nach Änderungen über `dotnet user-secrets remove` oder vereinfacht `dotnet user-secrets set` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-278">The JSON structure is flattened after modifications via `dotnet user-secrets remove` or `dotnet user-secrets set`.</span></span> <span data-ttu-id="86ad7-279">Beispielsweise wird durch `dotnet user-secrets remove "Movies:ConnectionString"` das Ausführen des `Movies` Objektliterals reduziert.</span><span class="sxs-lookup"><span data-stu-id="86ad7-279">For example, running `dotnet user-secrets remove "Movies:ConnectionString"` collapses the `Movies` object literal.</span></span> <span data-ttu-id="86ad7-280">Die geänderte Datei ähnelt der folgenden JSON-Datei:</span><span class="sxs-lookup"><span data-stu-id="86ad7-280">The modified file resembles the following JSON:</span></span>

```json
{
  "Movies:ServiceApiKey": "12345"
}
```

## <a name="set-multiple-secrets"></a><span data-ttu-id="86ad7-281">Festlegen mehrerer geheimer Schlüssel</span><span class="sxs-lookup"><span data-stu-id="86ad7-281">Set multiple secrets</span></span>

<span data-ttu-id="86ad7-282">Ein Batch geheimer Schlüssel kann durch die Weiterleitung von JSON an den Befehl festgelegt werden `set` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-282">A batch of secrets can be set by piping JSON to the `set` command.</span></span> <span data-ttu-id="86ad7-283">Im folgenden Beispiel werden die *input.jsfür* den Inhalt der Datei an den Befehl weitergeleitet `set` .</span><span class="sxs-lookup"><span data-stu-id="86ad7-283">In the following example, the *input.json* file's contents are piped to the `set` command.</span></span>

# <a name="windows"></a>[<span data-ttu-id="86ad7-284">Windows</span><span class="sxs-lookup"><span data-stu-id="86ad7-284">Windows</span></span>](#tab/windows)

<span data-ttu-id="86ad7-285">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="86ad7-285">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  type .\input.json | dotnet user-secrets set
  ```

# <a name="linux--macos"></a>[<span data-ttu-id="86ad7-286">Linux/macOS</span><span class="sxs-lookup"><span data-stu-id="86ad7-286">Linux / macOS</span></span>](#tab/linux+macos)

<span data-ttu-id="86ad7-287">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="86ad7-287">Open a command shell, and execute the following command:</span></span>

  ```dotnetcli
  cat ./input.json | dotnet user-secrets set
  ```

---

## <a name="access-a-secret"></a><span data-ttu-id="86ad7-288">Auf einen geheimen Schlüssel zugreifen</span><span class="sxs-lookup"><span data-stu-id="86ad7-288">Access a secret</span></span>

<span data-ttu-id="86ad7-289">Die [Konfigurations-API](xref:fundamentals/configuration/index) ermöglicht den Zugriff auf Benutzer Geheimnisse.</span><span class="sxs-lookup"><span data-stu-id="86ad7-289">The [Configuration API](xref:fundamentals/configuration/index) provides access to user secrets.</span></span>

<span data-ttu-id="86ad7-290">Wenn das Projekt .NET Framework als Ziel hat, installieren Sie die [Microsoft.Extensions.Config-urung. Usersecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) -nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="86ad7-290">If your project targets .NET Framework, install the [Microsoft.Extensions.Configuration.UserSecrets](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.UserSecrets) NuGet package.</span></span>

<span data-ttu-id="86ad7-291">In ASP.net Core 2,0 oder höher wird die Konfigurations Quelle "Benutzer Geheimnisse" automatisch im Entwicklungsmodus hinzugefügt, wenn das Projekt aufruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> .</span><span class="sxs-lookup"><span data-stu-id="86ad7-291">In ASP.NET Core 2.0 or later, the user secrets configuration source is automatically added in development mode when the project calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>.</span></span> <span data-ttu-id="86ad7-292">`CreateDefaultBuilder` wird aufgerufen, <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> wenn Folgendes <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> ist <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development> :</span><span class="sxs-lookup"><span data-stu-id="86ad7-292">`CreateDefaultBuilder` calls <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> when the <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName> is <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Program.cs?name=snippet_CreateWebHostBuilder&highlight=2)]

<span data-ttu-id="86ad7-293">Wenn `CreateDefaultBuilder` nicht aufgerufen wird, fügen Sie die Konfigurations Quelle für Benutzer Geheimnisse explizit hinzu, indem Sie <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> im- `Startup` Konstruktor aufrufen.</span><span class="sxs-lookup"><span data-stu-id="86ad7-293">When `CreateDefaultBuilder` isn't called, add the user secrets configuration source explicitly by calling <xref:Microsoft.Extensions.Configuration.UserSecretsConfigurationExtensions.AddUserSecrets%2A> in the `Startup` constructor.</span></span> <span data-ttu-id="86ad7-294">`AddUserSecrets`Wird nur aufgerufen, wenn die app in der Entwicklungsumgebung ausgeführt wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="86ad7-294">Call `AddUserSecrets` only when the app runs in the Development environment, as shown in the following example:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_StartupConstructor&highlight=12)]

<span data-ttu-id="86ad7-295">Benutzer Geheimnisse können über die .NET-Konfigurations-API abgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="86ad7-295">User secrets can be retrieved via the .NET Configuration API:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup.cs?name=snippet_StartupClass&highlight=14)]

## <a name="map-secrets-to-a-poco"></a><span data-ttu-id="86ad7-296">Zuordnen von Geheimnissen zu einem poco</span><span class="sxs-lookup"><span data-stu-id="86ad7-296">Map secrets to a POCO</span></span>

<span data-ttu-id="86ad7-297">Das Mapping eines gesamten Objektliterals zu einem poco (eine einfache .NET-Klasse mit-Eigenschaften) ist nützlich für das aggregierten von verknüpften Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="86ad7-297">Mapping an entire object literal to a POCO (a simple .NET class with properties) is useful for aggregating related properties.</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="86ad7-298">Um die vorangehenden geheimen Schlüssel einem poco zuzuordnen, verwenden Sie die [Objekt Diagramm-Bindungs](xref:fundamentals/configuration/index#bind-to-an-object-graph) Funktion der .NET-Konfigurations-API.</span><span class="sxs-lookup"><span data-stu-id="86ad7-298">To map the preceding secrets to a POCO, use the .NET Configuration API's [object graph binding](xref:fundamentals/configuration/index#bind-to-an-object-graph) feature.</span></span> <span data-ttu-id="86ad7-299">Der folgende Code bindet an ein benutzerdefiniertes `MovieSettings` poco und greift auf den- `ServiceApiKey` Eigenschafts Wert zu:</span><span class="sxs-lookup"><span data-stu-id="86ad7-299">The following code binds to a custom `MovieSettings` POCO and accesses the `ServiceApiKey` property value:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup3.cs?name=snippet_BindToObjectGraph)]

<span data-ttu-id="86ad7-300">Die `Movies:ConnectionString` `Movies:ServiceApiKey` Geheimnisse und werden den entsprechenden Eigenschaften in zugeordnet `MovieSettings` :</span><span class="sxs-lookup"><span data-stu-id="86ad7-300">The `Movies:ConnectionString` and `Movies:ServiceApiKey` secrets are mapped to the respective properties in `MovieSettings`:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Models/MovieSettings.cs?name=snippet_MovieSettingsClass)]

## <a name="string-replacement-with-secrets"></a><span data-ttu-id="86ad7-301">Zeichen folgen Ersetzung mit geheimen Schlüsseln</span><span class="sxs-lookup"><span data-stu-id="86ad7-301">String replacement with secrets</span></span>

<span data-ttu-id="86ad7-302">Das Speichern von Kenn Wörtern im Klartext ist unsicher.</span><span class="sxs-lookup"><span data-stu-id="86ad7-302">Storing passwords in plain text is insecure.</span></span> <span data-ttu-id="86ad7-303">Beispielsweise kann eine Datenbank-Verbindungs Zeichenfolge, die in gespeichert ist, *appsettings.json* ein Kennwort für den angegebenen Benutzer enthalten:</span><span class="sxs-lookup"><span data-stu-id="86ad7-303">For example, a database connection string stored in *appsettings.json* may include a password for the specified user:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings-unsecure.json?highlight=3)]

<span data-ttu-id="86ad7-304">Ein sichereren Ansatz besteht darin, das Kennwort als geheimen Schlüssel zu speichern.</span><span class="sxs-lookup"><span data-stu-id="86ad7-304">A more secure approach is to store the password as a secret.</span></span> <span data-ttu-id="86ad7-305">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="86ad7-305">For example:</span></span>

```dotnetcli
dotnet user-secrets set "DbPassword" "pass123"
```

<span data-ttu-id="86ad7-306">Entfernen Sie das `Password` Schlüssel-Wert-Paar aus der Verbindungs Zeichenfolge in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="86ad7-306">Remove the `Password` key-value pair from the connection string in *appsettings.json*.</span></span> <span data-ttu-id="86ad7-307">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="86ad7-307">For example:</span></span>

[!code-json[](app-secrets/samples/2.x/UserSecrets/appsettings.json?highlight=3)]

<span data-ttu-id="86ad7-308">Der Wert des Geheimnisses kann für die-Eigenschaft eines-Objekts festgelegt werden <xref:System.Data.SqlClient.SqlConnectionStringBuilder> <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> , um die Verbindungs Zeichenfolge abzuschließen:</span><span class="sxs-lookup"><span data-stu-id="86ad7-308">The secret's value can be set on a <xref:System.Data.SqlClient.SqlConnectionStringBuilder> object's <xref:System.Data.SqlClient.SqlConnectionStringBuilder.Password%2A> property to complete the connection string:</span></span>

[!code-csharp[](app-secrets/samples/2.x/UserSecrets/Startup2.cs?name=snippet_StartupClass&highlight=14-17)]

## <a name="list-the-secrets"></a><span data-ttu-id="86ad7-309">Auflisten der geheimen Schlüssel</span><span class="sxs-lookup"><span data-stu-id="86ad7-309">List the secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="86ad7-310">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="86ad7-310">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets list
```

<span data-ttu-id="86ad7-311">Die folgende Ausgabe wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="86ad7-311">The following output appears:</span></span>

```console
Movies:ConnectionString = Server=(localdb)\mssqllocaldb;Database=Movie-1;Trusted_Connection=True;MultipleActiveResultSets=true
Movies:ServiceApiKey = 12345
```

<span data-ttu-id="86ad7-312">Im vorherigen Beispiel gibt ein Doppelpunkt in den Schlüsselnamen die Objekthierarchie in *secrets.jsan*.</span><span class="sxs-lookup"><span data-stu-id="86ad7-312">In the preceding example, a colon in the key names denotes the object hierarchy within *secrets.json*.</span></span>

## <a name="remove-a-single-secret"></a><span data-ttu-id="86ad7-313">Entfernen eines einzelnen geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="86ad7-313">Remove a single secret</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="86ad7-314">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="86ad7-314">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets remove "Movies:ConnectionString"
```

<span data-ttu-id="86ad7-315">Die *secrets.jsfür* die Datei der App wurde geändert, um das Schlüssel-Wert-Paar zu entfernen, das dem Schlüssel zugeordnet ist `MoviesConnectionString` :</span><span class="sxs-lookup"><span data-stu-id="86ad7-315">The app's *secrets.json* file was modified to remove the key-value pair associated with the `MoviesConnectionString` key:</span></span>

```json
{
  "Movies": {
    "ServiceApiKey": "12345"
  }
}
```

<span data-ttu-id="86ad7-316">Wird ausgeführt, wird `dotnet user-secrets list` die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="86ad7-316">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
Movies:ServiceApiKey = 12345
```

## <a name="remove-all-secrets"></a><span data-ttu-id="86ad7-317">Alle geheimen Schlüssel entfernen</span><span class="sxs-lookup"><span data-stu-id="86ad7-317">Remove all secrets</span></span>

[!INCLUDE[secrets.json file](~/includes/app-secrets/secrets-json-file-and-text.md)]

<span data-ttu-id="86ad7-318">Führen Sie den folgenden Befehl aus dem Verzeichnis aus, in dem die Projektdatei vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="86ad7-318">Run the following command from the directory in which the project file exists:</span></span>

```dotnetcli
dotnet user-secrets clear
```

<span data-ttu-id="86ad7-319">Alle geheimen Benutzerschlüssel für die APP wurden aus dem *secrets.js* für die Datei gelöscht:</span><span class="sxs-lookup"><span data-stu-id="86ad7-319">All user secrets for the app have been deleted from the *secrets.json* file:</span></span>

```json
{}
```

<span data-ttu-id="86ad7-320">Wird ausgeführt, wird `dotnet user-secrets list` die folgende Meldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="86ad7-320">Running `dotnet user-secrets list` displays the following message:</span></span>

```console
No secrets configured for this application.
```

## <a name="additional-resources"></a><span data-ttu-id="86ad7-321">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="86ad7-321">Additional resources</span></span>

* <span data-ttu-id="86ad7-322">Informationen zum Zugriff auf Benutzer Geheimnisse aus IIS finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/16328) .</span><span class="sxs-lookup"><span data-stu-id="86ad7-322">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/16328) for information on accessing user secrets from IIS.</span></span>
* <xref:fundamentals/configuration/index>
* <xref:security/key-vault-configuration>

::: moniker-end