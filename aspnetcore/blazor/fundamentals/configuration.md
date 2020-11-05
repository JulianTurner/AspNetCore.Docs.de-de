---
title: Blazor-Konfiguration in ASP.NET Core
author: guardrex
description: In diesem Artikel erhalten Sie weitere Informationen zur Konfiguration von Blazor-Apps, einschließlich der App-Einstellungen, der Authentifizierung und Protokollierungskonfiguration.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/29/2020
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
ms.openlocfilehash: f8b1c49ab29bb8a88ca6d9785cd7ee151315e065
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234373"
---
# <a name="aspnet-core-no-locblazor-configuration"></a>Blazor-Konfiguration in ASP.NET Core

> [!NOTE]
> Dieses Thema gilt für Blazor WebAssembly. Eine allgemeine Anleitung zur App-Konfiguration in ASP.NET Core finden Sie unter <xref:fundamentals/configuration/index>.

Blazor WebAssembly lädt die Konfiguration standardmäßig aus den App-Einstellungsdateien:

* `wwwroot/appsettings.json`
* `wwwroot/appsettings.{ENVIRONMENT}.json`

Andere Konfigurationsanbieter, die von der App registriert werden, können ebenfalls eine Konfiguration bereitstellen.

Nicht alle Anbieter oder Anbieterfeatures sind für Blazor WebAssembly-Apps geeignet:

* [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration): Der Anbieter wird für die verwaltete Identität und die Anwendungs-ID-Szenarien (Client-ID) mit geheimem Clientschlüssel nicht unterstützt. Die Anwendungs-ID mit einem geheimen Clientschlüssel wird nicht für ASP.NET Core-Apps empfohlen, insbesondere nicht für Blazor WebAssembly-Apps, da der geheime Clientschlüssel nicht clientseitig für den Zugriff auf den Dienst geschützt werden kann.
* [Azure-App-Konfigurationsanbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app): Der Anbieter eignet sich nicht für Blazor WebAssembly-Apps, da Blazor WebAssembly-Apps nicht auf einem Server in Azure ausgeführt werden.

> [!WARNING]
> Die Konfiguration in einer Blazor WebAssembly-App ist für Benutzer sichtbar. **Speichern Sie keine App-Geheimnisse oder -Anmeldeinformationen in der Konfiguration.**

Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.

## <a name="app-settings-configuration"></a>Konfiguration von App-Einstellungen

`wwwroot/appsettings.json`:

```json
{
  "message": "Hello from config!"
}
```

Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:

```razor
@page "/"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1>Configuration example</h1>

<p>Message: @Configuration["message"]</p>
```

## <a name="custom-configuration-provider-with-ef-core"></a>Benutzerdefinierter Konfigurationsanbieter mit EF Core

Der benutzerdefinierte Konfigurationsanbieter mit EF Core, der in <xref:fundamentals/configuration/index#custom-configuration-provider> gezeigt wird, funktioniert mit Blazor WebAssembly-Apps.

Fügen Sie den Konfigurationsanbieter des Beispiels mit dem folgenden Code in `Program.Main` (`Program.cs`) hinzu:

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:

```razor
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<ul>
    <li>@Configuration["quote1"]</li>
    <li>@Configuration["quote2"]</li>
    <li>@Configuration["quote3"]</li>
</ul>
```

## <a name="memory-configuration-source"></a>Arbeitsspeicher als Konfigurationsquelle

Im folgenden Beispiel wird ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element verwendet, um zusätzliche Konfiguration bereitzustellen:

`Program.Main`:

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

Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen:

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

Um andere Konfigurationsdateien aus dem Ordner `wwwroot` in die Konfiguration einzulesen, verwenden Sie einen <xref:System.Net.Http.HttpClient>, um den Inhalt der Datei abzurufen. Bei diesem Ansatz kann die vorhandene <xref:System.Net.Http.HttpClient>-Dienstregistrierung den zum Lesen der Datei erstellten lokalen Client verwenden, wie im folgenden Beispiel gezeigt:

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

`Program.Main`:

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

## <a name="authentication-configuration"></a>Authentifizierungskonfiguration

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

`Program.Main`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Konfiguration der Protokollierung

Fügen Sie einen Paketverweis für [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) hinzu:

```xml
<PackageReference Include="Microsoft.Extensions.Logging.Configuration" Version="{VERSION}" />
```

Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) ermittelt werden.

`wwwroot/appsettings.json`:

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

`Program.Main`:

```csharp
using Microsoft.Extensions.Logging;

...

builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Konfiguration des Host-Generators

`Program.Main`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Zwischengespeicherte Konfiguration

Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert. Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren. Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:

* Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.
* Die Dateien `service-worker.js` und `service-worker-assets.js` der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.

Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.
