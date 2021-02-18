---
title: Blazor-Konfiguration in ASP.NET Core
author: guardrex
description: In diesem Artikel erhalten Sie weitere Informationen zur Konfiguration von Blazor-Apps, einschließlich der App-Einstellungen, der Authentifizierung und Protokollierungskonfiguration.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/10/2020
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
uid: blazor/fundamentals/configuration
ms.openlocfilehash: 48d78f40e9254bac182ffbc534550157664bcc5b
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106933"
---
# <a name="aspnet-core-blazor-configuration"></a><span data-ttu-id="eec9c-103">Blazor-Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eec9c-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="eec9c-104">Dieses Thema gilt für Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="eec9c-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="eec9c-105">Eine allgemeine Anleitung zur App-Konfiguration in ASP.NET Core finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="eec9c-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="eec9c-106">Blazor WebAssembly lädt die Konfiguration standardmäßig aus den folgenden App-Einstellungsdateien:</span><span class="sxs-lookup"><span data-stu-id="eec9c-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="eec9c-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="eec9c-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="eec9c-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, wobei der Platzhalter `{ENVIRONMENT}` die [Laufzeitumgebung](xref:fundamentals/environments) der App ist.</span><span class="sxs-lookup"><span data-stu-id="eec9c-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="eec9c-109">Andere in der App registrierte Konfigurationsanbieter können auch Konfigurationen bereitstellen, aber nicht alle Anbieter und Anbieterfunktionen eignen sich für Blazor WebAssembly-Apps:</span><span class="sxs-lookup"><span data-stu-id="eec9c-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="eec9c-110">[Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration): Der Anbieter wird für die verwaltete Identität und die Anwendungs-ID-Szenarien (Client-ID) mit geheimem Clientschlüssel nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="eec9c-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="eec9c-111">Die Anwendungs-ID mit einem geheimen Clientschlüssel wird nicht für ASP.NET Core-Apps empfohlen, insbesondere nicht für Blazor WebAssembly-Apps, da der geheime Clientschlüssel nicht clientseitig für den Zugriff auf den Azure Key Vault-Dienst geschützt werden kann.</span><span class="sxs-lookup"><span data-stu-id="eec9c-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="eec9c-112">[Azure-App-Konfigurationsanbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app): Der Anbieter eignet sich nicht für Blazor WebAssembly-Apps, da Blazor WebAssembly-Apps nicht auf einem Server in Azure ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="eec9c-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="eec9c-113">Die Konfiguration in einer Blazor WebAssembly-App ist für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="eec9c-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="eec9c-114">**Speichern Sie keine App-Geheimnisse, Anmeldeinformationen oder andere vertrauliche Daten in der Konfiguration einer Blazor WebAssembly-App.**</span><span class="sxs-lookup"><span data-stu-id="eec9c-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="eec9c-115">Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="eec9c-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="eec9c-116">Konfiguration von App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="eec9c-116">App settings configuration</span></span>

<span data-ttu-id="eec9c-117">Die Konfiguration in App-Einstellungsdateien wird standardmäßig geladen.</span><span class="sxs-lookup"><span data-stu-id="eec9c-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="eec9c-118">Im folgenden Beispiel wird ein Benutzeroberflächen-Konfigurationswert in einer App-Einstellungsdatei gespeichert und automatisch vom Blazor-Framework geladen.</span><span class="sxs-lookup"><span data-stu-id="eec9c-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="eec9c-119">Der Wert wird von einer Komponente gelesen.</span><span class="sxs-lookup"><span data-stu-id="eec9c-119">The value is read by a component.</span></span>

<span data-ttu-id="eec9c-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="eec9c-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="eec9c-121">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="eec9c-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="eec9c-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="eec9c-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="eec9c-123">Um andere Konfigurationsdateien aus dem Ordner `wwwroot` in die Konfiguration einzulesen, verwenden Sie einen <xref:System.Net.Http.HttpClient>, um den Inhalt der Datei abzurufen.</span><span class="sxs-lookup"><span data-stu-id="eec9c-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="eec9c-124">Im folgenden Beispiel wird eine Konfigurationsdatei (`cars.json`) in die Konfiguration der App gelesen.</span><span class="sxs-lookup"><span data-stu-id="eec9c-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="eec9c-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="eec9c-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="eec9c-126">Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="eec9c-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="eec9c-127">Ändern Sie in `Program.Main` von `Program.cs` die vorhandene <xref:System.Net.Http.HttpClient>-Dienstregistrierung so, dass der Client zum Lesen der Datei verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="eec9c-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

```csharp
var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="memory-configuration-source"></a><span data-ttu-id="eec9c-128">Arbeitsspeicher als Konfigurationsquelle</span><span class="sxs-lookup"><span data-stu-id="eec9c-128">Memory Configuration Source</span></span>

<span data-ttu-id="eec9c-129">Im folgenden Beispiel wird ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element in `Program.Main` verwendet, um eine zusätzliche Konfiguration bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="eec9c-129">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="eec9c-130">Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="eec9c-130">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="eec9c-131">In `Program.Main` von `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="eec9c-131">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var vehicleData = new Dictionary<string, string>()
{
    { "color", "blue" },
    { "type", "car" },
    { "wheels:count", "3" },
    { "wheels:brand", "Blazin" },
    { "wheels:brand:type", "rally" },
    { "wheels:year", "2008" },
};

var memoryConfig = new MemoryConfigurationSource { InitialData = vehicleData };

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="eec9c-132">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="eec9c-132">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="eec9c-133">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="eec9c-133">`Pages/MemoryConfig.razor`:</span></span>

```razor
@page "/memory-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Memory configuration example</h1>

<h2>General specifications</h2>

<ul>
    <li>Color: @Configuration["color"]</li>
    <li>Type: @Configuration["type"]</li>
</ul>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>
```

<span data-ttu-id="eec9c-134">Rufen Sie einen Abschnitt der Konfiguration in C#-Code mit <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> ab.</span><span class="sxs-lookup"><span data-stu-id="eec9c-134">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="eec9c-135">Im folgenden Beispiel wird der `wheels`-Abschnitt für die Konfiguration im vorherigen Beispiel abgerufen:</span><span class="sxs-lookup"><span data-stu-id="eec9c-135">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="eec9c-136">Authentifizierungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="eec9c-136">Authentication configuration</span></span>

<span data-ttu-id="eec9c-137">Geben Sie die Authentifizierungskonfiguration in einer App-Einstellungsdatei an.</span><span class="sxs-lookup"><span data-stu-id="eec9c-137">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="eec9c-138">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="eec9c-138">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="eec9c-139">Laden Sie die Konfiguration für einen Identity-Anbieter mit <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="eec9c-139">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="eec9c-140">Im folgenden Beispiel wird die Konfiguration für einen [OIDC-Anbieter](xref:blazor/security/webassembly/standalone-with-authentication-library) geladen.</span><span class="sxs-lookup"><span data-stu-id="eec9c-140">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="eec9c-141">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="eec9c-141">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="eec9c-142">Konfiguration der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="eec9c-142">Logging configuration</span></span>

<span data-ttu-id="eec9c-143">Fügen Sie einen Paketverweis für [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) in der Projektdatei der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="eec9c-143">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="eec9c-144">Geben Sie in der App-Einstellungsdatei die Protokollierungskonfiguration an.</span><span class="sxs-lookup"><span data-stu-id="eec9c-144">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="eec9c-145">Die Protokollierungskonfiguration wird in `Program.Main` geladen.</span><span class="sxs-lookup"><span data-stu-id="eec9c-145">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="eec9c-146">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="eec9c-146">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```

<span data-ttu-id="eec9c-147">Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.Logging?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="eec9c-147">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="eec9c-148">In `Program.Main` von `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="eec9c-148">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="eec9c-149">Konfiguration des Host-Generators</span><span class="sxs-lookup"><span data-stu-id="eec9c-149">Host builder configuration</span></span>

<span data-ttu-id="eec9c-150">Lesen Sie die Host-Generatorkonfiguration aus <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="eec9c-150">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="eec9c-151">In `Program.Main` von `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="eec9c-151">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="eec9c-152">Zwischengespeicherte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="eec9c-152">Cached configuration</span></span>

<span data-ttu-id="eec9c-153">Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="eec9c-153">Configuration files are cached for offline use.</span></span> <span data-ttu-id="eec9c-154">Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="eec9c-154">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="eec9c-155">Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="eec9c-155">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="eec9c-156">Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.</span><span class="sxs-lookup"><span data-stu-id="eec9c-156">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="eec9c-157">Die Dateien `service-worker.js` und `service-worker-assets.js` der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="eec9c-157">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="eec9c-158">Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="eec9c-158">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
