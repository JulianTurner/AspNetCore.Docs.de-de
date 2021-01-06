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
ms.openlocfilehash: 5889d775c09ee23f19bf3ff59344c52d469c4bdc
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485966"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="0eafa-103">Blazor-Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0eafa-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="0eafa-104">Dieses Thema gilt für Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="0eafa-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="0eafa-105">Eine allgemeine Anleitung zur App-Konfiguration in ASP.NET Core finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0eafa-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="0eafa-106">Blazor WebAssembly lädt die Konfiguration standardmäßig aus den folgenden App-Einstellungsdateien:</span><span class="sxs-lookup"><span data-stu-id="0eafa-106">Blazor WebAssembly loads configuration from the following app settings files by default:</span></span>

* <span data-ttu-id="0eafa-107">`wwwroot/appsettings.json`.</span><span class="sxs-lookup"><span data-stu-id="0eafa-107">`wwwroot/appsettings.json`.</span></span>
* <span data-ttu-id="0eafa-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, wobei der Platzhalter `{ENVIRONMENT}` die [Laufzeitumgebung](xref:fundamentals/environments) der App ist.</span><span class="sxs-lookup"><span data-stu-id="0eafa-108">`wwwroot/appsettings.{ENVIRONMENT}.json`, where the `{ENVIRONMENT}` placeholder is the app's [runtime environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0eafa-109">Andere in der App registrierte Konfigurationsanbieter können auch Konfigurationen bereitstellen, aber nicht alle Anbieter und Anbieterfunktionen eignen sich für Blazor WebAssembly-Apps:</span><span class="sxs-lookup"><span data-stu-id="0eafa-109">Other configuration providers registered by the app can also provide configuration, but not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="0eafa-110">[Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration): Der Anbieter wird für die verwaltete Identität und die Anwendungs-ID-Szenarien (Client-ID) mit geheimem Clientschlüssel nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="0eafa-110">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="0eafa-111">Die Anwendungs-ID mit einem geheimen Clientschlüssel wird nicht für ASP.NET Core-Apps empfohlen, insbesondere nicht für Blazor WebAssembly-Apps, da der geheime Clientschlüssel nicht clientseitig für den Zugriff auf den Azure Key Vault-Dienst geschützt werden kann.</span><span class="sxs-lookup"><span data-stu-id="0eafa-111">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access the Azure Key Vault service.</span></span>
* <span data-ttu-id="0eafa-112">[Azure-App-Konfigurationsanbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app): Der Anbieter eignet sich nicht für Blazor WebAssembly-Apps, da Blazor WebAssembly-Apps nicht auf einem Server in Azure ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="0eafa-112">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="0eafa-113">Die Konfiguration in einer Blazor WebAssembly-App ist für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="0eafa-113">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="0eafa-114">**Speichern Sie keine App-Geheimnisse, Anmeldeinformationen oder andere vertrauliche Daten in der Konfiguration einer Blazor WebAssembly-App.**</span><span class="sxs-lookup"><span data-stu-id="0eafa-114">**Don't store app secrets, credentials, or any other sensitive data in the configuration of a Blazor WebAssembly app.**</span></span>

<span data-ttu-id="0eafa-115">Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="0eafa-115">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="0eafa-116">Konfiguration von App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="0eafa-116">App settings configuration</span></span>

<span data-ttu-id="0eafa-117">Die Konfiguration in App-Einstellungsdateien wird standardmäßig geladen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-117">Configuration in app settings files are loaded by default.</span></span> <span data-ttu-id="0eafa-118">Im folgenden Beispiel wird ein Benutzeroberflächen-Konfigurationswert in einer App-Einstellungsdatei gespeichert und automatisch vom Blazor-Framework geladen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-118">In the following example, a UI configuration value is stored in an app settings file and loaded by the Blazor framework automatically.</span></span> <span data-ttu-id="0eafa-119">Der Wert wird von einer Komponente gelesen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-119">The value is read by a component.</span></span>

<span data-ttu-id="0eafa-120">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-120">`wwwroot/appsettings.json`:</span></span>

```json
{
  "h1FontSize": "50px"
}
```

<span data-ttu-id="0eafa-121">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="0eafa-122">`Pages/ConfigurationExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-122">`Pages/ConfigurationExample.razor`:</span></span>

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

<span data-ttu-id="0eafa-123">Um andere Konfigurationsdateien aus dem Ordner `wwwroot` in die Konfiguration einzulesen, verwenden Sie einen <xref:System.Net.Http.HttpClient>, um den Inhalt der Datei abzurufen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-123">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="0eafa-124">Im folgenden Beispiel wird eine Konfigurationsdatei (`cars.json`) in die Konfiguration der App gelesen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-124">The following example reads a configuration file (`cars.json`) into the app's configuration.</span></span>

<span data-ttu-id="0eafa-125">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-125">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="0eafa-126">Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="0eafa-126">Add the namespace for <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;
```

<span data-ttu-id="0eafa-127">Ändern Sie in `Program.Main` von `Program.cs` die vorhandene <xref:System.Net.Http.HttpClient>-Dienstregistrierung so, dass der Client zum Lesen der Datei verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="0eafa-127">In `Program.Main` of `Program.cs`, modify the existing <xref:System.Net.Http.HttpClient> service registration to use the client to read the file:</span></span>

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

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="0eafa-128">Benutzerdefinierter Konfigurationsanbieter mit EF Core</span><span class="sxs-lookup"><span data-stu-id="0eafa-128">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="0eafa-129">Der benutzerdefinierte Konfigurationsanbieter mit EF Core, der in <xref:fundamentals/configuration/index#custom-configuration-provider> gezeigt wird, funktioniert mit Blazor WebAssembly-Apps.</span><span class="sxs-lookup"><span data-stu-id="0eafa-129">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

> [!WARNING]
> <span data-ttu-id="0eafa-130">Datenbank-Verbindungszeichenfolgen und Datenbanken, die mit Blazor WebAssembly-Apps geladen werden, sind nicht sicher und sollten nicht zum Speichern vertraulicher Daten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0eafa-130">Database connection strings and databases loaded with Blazor WebAssembly apps aren't secure and shouldn't be used to store sensitive data.</span></span>

<span data-ttu-id="0eafa-131">Fügen Sie der Projektdatei der App Paketverweise für [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) und [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) hinzu.</span><span class="sxs-lookup"><span data-stu-id="0eafa-131">Add package references for [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) and [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) to the app's project file.</span></span>

<span data-ttu-id="0eafa-132">Fügen Sie die Entity Framework Core-Konfigurationsklassen hinzu, die unter <xref:fundamentals/configuration/index#custom-configuration-provider> beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="0eafa-132">Add the EF Core configuration classes described in <xref:fundamentals/configuration/index#custom-configuration-provider>.</span></span>

<span data-ttu-id="0eafa-133">Fügen Sie `Program.cs` die Namespaces für <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> und <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="0eafa-133">Add namespaces for <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> and <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="0eafa-134">In `Program.Main` von `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-134">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="0eafa-135">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-135">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="0eafa-136">`Pages/EFCoreConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-136">`Pages/EFCoreConfig.razor`:</span></span>

```razor
@page "/efcore-config"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>EF Core configuration example</h1>

<h2>Quotes</h2>

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>

<p>
    Quotes &copy;2005 
    <a href="https://www.uphe.com/">Universal Pictures</a>: 
    <a href="https://www.uphe.com/movies/serenity">Serenity</a>
</p>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="0eafa-137">Arbeitsspeicher als Konfigurationsquelle</span><span class="sxs-lookup"><span data-stu-id="0eafa-137">Memory Configuration Source</span></span>

<span data-ttu-id="0eafa-138">Im folgenden Beispiel wird ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element in `Program.Main` verwendet, um eine zusätzliche Konfiguration bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-138">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> in `Program.Main` to supply additional configuration.</span></span>

<span data-ttu-id="0eafa-139">Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="0eafa-139">Add the namespace for <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

<span data-ttu-id="0eafa-140">In `Program.Main` von `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-140">In `Program.Main` of `Program.cs`:</span></span>

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

<span data-ttu-id="0eafa-141">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-141">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data.</span></span>

<span data-ttu-id="0eafa-142">`Pages/MemoryConfig.razor`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-142">`Pages/MemoryConfig.razor`:</span></span>

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

<span data-ttu-id="0eafa-143">Rufen Sie einen Abschnitt der Konfiguration in C#-Code mit <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> ab.</span><span class="sxs-lookup"><span data-stu-id="0eafa-143">Obtain a section of the configuration in C# code with <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="0eafa-144">Im folgenden Beispiel wird der `wheels`-Abschnitt für die Konfiguration im vorherigen Beispiel abgerufen:</span><span class="sxs-lookup"><span data-stu-id="0eafa-144">The following example obtains the `wheels` section for the configuration in the preceding example:</span></span>

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a><span data-ttu-id="0eafa-145">Authentifizierungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="0eafa-145">Authentication configuration</span></span>

<span data-ttu-id="0eafa-146">Geben Sie die Authentifizierungskonfiguration in einer App-Einstellungsdatei an.</span><span class="sxs-lookup"><span data-stu-id="0eafa-146">Provide authentication configuration in an app settings file.</span></span>

<span data-ttu-id="0eafa-147">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-147">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="0eafa-148">Laden Sie die Konfiguration für einen Identity-Anbieter mit <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="0eafa-148">Load the configuration for an Identity provider with <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`.</span></span> <span data-ttu-id="0eafa-149">Im folgenden Beispiel wird die Konfiguration für einen [OIDC-Anbieter](xref:blazor/security/webassembly/standalone-with-authentication-library) geladen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-149">The following example loads configuration for an [OIDC provider](xref:blazor/security/webassembly/standalone-with-authentication-library).</span></span>

<span data-ttu-id="0eafa-150">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-150">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="0eafa-151">Konfiguration der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="0eafa-151">Logging configuration</span></span>

<span data-ttu-id="0eafa-152">Fügen Sie einen Paketverweis für [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) in der Projektdatei der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="0eafa-152">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) to the app's project file.</span></span> <span data-ttu-id="0eafa-153">Geben Sie in der App-Einstellungsdatei die Protokollierungskonfiguration an.</span><span class="sxs-lookup"><span data-stu-id="0eafa-153">In the app settings file, provide logging configuration.</span></span> <span data-ttu-id="0eafa-154">Die Protokollierungskonfiguration wird in `Program.Main` geladen.</span><span class="sxs-lookup"><span data-stu-id="0eafa-154">The logging configuration is loaded in `Program.Main`.</span></span>

<span data-ttu-id="0eafa-155">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-155">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="0eafa-156">Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.Logging?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="0eafa-156">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.Extensions.Logging;
```

<span data-ttu-id="0eafa-157">In `Program.Main` von `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-157">In `Program.Main` of `Program.cs`:</span></span>

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="0eafa-158">Konfiguration des Host-Generators</span><span class="sxs-lookup"><span data-stu-id="0eafa-158">Host builder configuration</span></span>

<span data-ttu-id="0eafa-159">Lesen Sie die Host-Generatorkonfiguration aus <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="0eafa-159">Read host builder configuration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.</span></span>

<span data-ttu-id="0eafa-160">In `Program.Main` von `Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0eafa-160">In `Program.Main` of `Program.cs`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="0eafa-161">Zwischengespeicherte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="0eafa-161">Cached configuration</span></span>

<span data-ttu-id="0eafa-162">Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="0eafa-162">Configuration files are cached for offline use.</span></span> <span data-ttu-id="0eafa-163">Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="0eafa-163">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="0eafa-164">Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="0eafa-164">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="0eafa-165">Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.</span><span class="sxs-lookup"><span data-stu-id="0eafa-165">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="0eafa-166">Die Dateien `service-worker.js` und `service-worker-assets.js` der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="0eafa-166">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="0eafa-167">Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="0eafa-167">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
