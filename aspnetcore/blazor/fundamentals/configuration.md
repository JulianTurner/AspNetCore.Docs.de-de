---
title: Blazor-Konfiguration in ASP.NET Core
author: guardrex
description: In diesem Artikel erhalten Sie weitere Informationen zur Konfiguration von Blazor-Apps, einschließlich der App-Einstellungen, der Authentifizierung und Protokollierungskonfiguration.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/fundamentals/configuration
ms.openlocfilehash: f8b1c49ab29bb8a88ca6d9785cd7ee151315e065
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234373"
---
# <a name="aspnet-core-no-locblazor-configuration"></a><span data-ttu-id="e7589-103">Blazor-Konfiguration in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7589-103">ASP.NET Core Blazor configuration</span></span>

> [!NOTE]
> <span data-ttu-id="e7589-104">Dieses Thema gilt für Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="e7589-104">This topic applies to Blazor WebAssembly.</span></span> <span data-ttu-id="e7589-105">Eine allgemeine Anleitung zur App-Konfiguration in ASP.NET Core finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e7589-105">For general guidance on ASP.NET Core app configuration, see <xref:fundamentals/configuration/index>.</span></span>

<span data-ttu-id="e7589-106">Blazor WebAssembly lädt die Konfiguration standardmäßig aus den App-Einstellungsdateien:</span><span class="sxs-lookup"><span data-stu-id="e7589-106">Blazor WebAssembly loads configuration from app settings files by default:</span></span>

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

<span data-ttu-id="e7589-107">Andere Konfigurationsanbieter, die von der App registriert werden, können ebenfalls eine Konfiguration bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="e7589-107">Other configuration providers registered by the app can also provide configuration.</span></span>

<span data-ttu-id="e7589-108">Nicht alle Anbieter oder Anbieterfeatures sind für Blazor WebAssembly-Apps geeignet:</span><span class="sxs-lookup"><span data-stu-id="e7589-108">Not all providers or provider features are appropriate for Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="e7589-109">[Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration): Der Anbieter wird für die verwaltete Identität und die Anwendungs-ID-Szenarien (Client-ID) mit geheimem Clientschlüssel nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e7589-109">[Azure Key Vault configuration provider](xref:security/key-vault-configuration): The provider isn't supported for managed identity and application ID (client ID) with client secret scenarios.</span></span> <span data-ttu-id="e7589-110">Die Anwendungs-ID mit einem geheimen Clientschlüssel wird nicht für ASP.NET Core-Apps empfohlen, insbesondere nicht für Blazor WebAssembly-Apps, da der geheime Clientschlüssel nicht clientseitig für den Zugriff auf den Dienst geschützt werden kann.</span><span class="sxs-lookup"><span data-stu-id="e7589-110">Application ID with a client secret isn't recommended for any ASP.NET Core app, especially Blazor WebAssembly apps because the client secret can't be secured client-side to access to the service.</span></span>
* <span data-ttu-id="e7589-111">[Azure-App-Konfigurationsanbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app): Der Anbieter eignet sich nicht für Blazor WebAssembly-Apps, da Blazor WebAssembly-Apps nicht auf einem Server in Azure ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e7589-111">[Azure App configuration provider](/azure/azure-app-configuration/quickstart-aspnet-core-app): The provider isn't appropriate for Blazor WebAssembly apps because Blazor WebAssembly apps don't run on a server in Azure.</span></span>

> [!WARNING]
> <span data-ttu-id="e7589-112">Die Konfiguration in einer Blazor WebAssembly-App ist für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="e7589-112">Configuration in a Blazor WebAssembly app is visible to users.</span></span> <span data-ttu-id="e7589-113">**Speichern Sie keine App-Geheimnisse oder -Anmeldeinformationen in der Konfiguration.**</span><span class="sxs-lookup"><span data-stu-id="e7589-113">**Don't store app secrets or credentials in configuration.**</span></span>

<span data-ttu-id="e7589-114">Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="e7589-114">For more information on configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="app-settings-configuration"></a><span data-ttu-id="e7589-115">Konfiguration von App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="e7589-115">App settings configuration</span></span>

<span data-ttu-id="e7589-116">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="e7589-116">`wwwroot/appsettings.json`:</span></span>

```json
{
  "message": "Hello from config!"
}
```

<span data-ttu-id="e7589-117">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="e7589-117">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a><span data-ttu-id="e7589-118">Benutzerdefinierter Konfigurationsanbieter mit EF Core</span><span class="sxs-lookup"><span data-stu-id="e7589-118">Custom configuration provider with EF Core</span></span>

<span data-ttu-id="e7589-119">Der benutzerdefinierte Konfigurationsanbieter mit EF Core, der in <xref:fundamentals/configuration/index#custom-configuration-provider> gezeigt wird, funktioniert mit Blazor WebAssembly-Apps.</span><span class="sxs-lookup"><span data-stu-id="e7589-119">The custom configuration provider with EF Core demonstrated in <xref:fundamentals/configuration/index#custom-configuration-provider> works with Blazor WebAssembly apps.</span></span>

<span data-ttu-id="e7589-120">Fügen Sie den Konfigurationsanbieter des Beispiels mit dem folgenden Code in `Program.Main` (`Program.cs`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="e7589-120">Add the example's configuration provider with the following code in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

<span data-ttu-id="e7589-121">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="e7589-121">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a><span data-ttu-id="e7589-122">Arbeitsspeicher als Konfigurationsquelle</span><span class="sxs-lookup"><span data-stu-id="e7589-122">Memory Configuration Source</span></span>

<span data-ttu-id="e7589-123">Im folgenden Beispiel wird ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element verwendet, um zusätzliche Konfiguration bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="e7589-123">The following example uses a <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource> to supply additional configuration:</span></span>

<span data-ttu-id="e7589-124">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e7589-124">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration.Memory;

...

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

...

builder.Configuration.Add(memoryConfig);
```

<span data-ttu-id="e7589-125">Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="e7589-125">Inject an <xref:Microsoft.Extensions.Configuration.IConfiguration> instance into a component to access the configuration data:</span></span>

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<h2>Wheels</h2>

<ul>
    <li>Count: @Configuration["wheels:count"]</li>
    <li>Brand: @Configuration["wheels:brand"]</li>
    <li>Type: @Configuration["wheels:brand:type"]</li>
    <li>Year: @Configuration["wheels:year"]</li>
</ul>

@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");
        
        ...
    }
}
```

<span data-ttu-id="e7589-126">Um andere Konfigurationsdateien aus dem Ordner `wwwroot` in die Konfiguration einzulesen, verwenden Sie einen <xref:System.Net.Http.HttpClient>, um den Inhalt der Datei abzurufen.</span><span class="sxs-lookup"><span data-stu-id="e7589-126">To read other configuration files from the `wwwroot` folder into configuration, use an <xref:System.Net.Http.HttpClient> to obtain the file's content.</span></span> <span data-ttu-id="e7589-127">Bei diesem Ansatz kann die vorhandene <xref:System.Net.Http.HttpClient>-Dienstregistrierung den zum Lesen der Datei erstellten lokalen Client verwenden, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="e7589-127">When using this approach, the existing <xref:System.Net.Http.HttpClient> service registration can use the local client created to read the file, as the following example shows:</span></span>

<span data-ttu-id="e7589-128">`wwwroot/cars.json`:</span><span class="sxs-lookup"><span data-stu-id="e7589-128">`wwwroot/cars.json`:</span></span>

```json
{
    "size": "tiny"
}
```

<span data-ttu-id="e7589-129">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e7589-129">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Configuration;

...

var http = new HttpClient()
{
    BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
};

builder.Services.AddScoped(sp => http);

using var response = await http.GetAsync("cars.json");
using var stream = await response.Content.ReadAsStreamAsync();

builder.Configuration.AddJsonStream(stream);
```

## <a name="authentication-configuration"></a><span data-ttu-id="e7589-130">Authentifizierungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="e7589-130">Authentication configuration</span></span>

<span data-ttu-id="e7589-131">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="e7589-131">`wwwroot/appsettings.json`:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="e7589-132">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e7589-132">`Program.Main`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a><span data-ttu-id="e7589-133">Konfiguration der Protokollierung</span><span class="sxs-lookup"><span data-stu-id="e7589-133">Logging configuration</span></span>

<span data-ttu-id="e7589-134">Fügen Sie einen Paketverweis für [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) hinzu:</span><span class="sxs-lookup"><span data-stu-id="e7589-134">Add a package reference for [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration):</span></span>

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

<span data-ttu-id="e7589-135">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="e7589-135">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration).</span></span>

<span data-ttu-id="e7589-136">`wwwroot/appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="e7589-136">`wwwroot/appsettings.json`:</span></span>

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

<span data-ttu-id="e7589-137">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e7589-137">`Program.Main`:</span></span>

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a><span data-ttu-id="e7589-138">Konfiguration des Host-Generators</span><span class="sxs-lookup"><span data-stu-id="e7589-138">Host builder configuration</span></span>

<span data-ttu-id="e7589-139">`Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="e7589-139">`Program.Main`:</span></span>

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a><span data-ttu-id="e7589-140">Zwischengespeicherte Konfiguration</span><span class="sxs-lookup"><span data-stu-id="e7589-140">Cached configuration</span></span>

<span data-ttu-id="e7589-141">Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="e7589-141">Configuration files are cached for offline use.</span></span> <span data-ttu-id="e7589-142">Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="e7589-142">With [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app), you can only update configuration files when creating a new deployment.</span></span> <span data-ttu-id="e7589-143">Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:</span><span class="sxs-lookup"><span data-stu-id="e7589-143">Editing configuration files between deployments has no effect because:</span></span>

* <span data-ttu-id="e7589-144">Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.</span><span class="sxs-lookup"><span data-stu-id="e7589-144">Users have cached versions of the files that they continue to use.</span></span>
* <span data-ttu-id="e7589-145">Die Dateien `service-worker.js` und `service-worker-assets.js` der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="e7589-145">The PWA's `service-worker.js` and `service-worker-assets.js` files must be rebuilt on compilation, which signal to the app on the user's next online visit that the app has been redeployed.</span></span>

<span data-ttu-id="e7589-146">Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.</span><span class="sxs-lookup"><span data-stu-id="e7589-146">For more information on how background updates are handled by PWAs, see <xref:blazor/progressive-web-app#background-updates>.</span></span>
