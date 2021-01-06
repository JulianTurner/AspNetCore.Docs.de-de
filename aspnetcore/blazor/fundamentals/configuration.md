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
# <a name="aspnet-core-no-locblazor-configuration"></a>Blazor-Konfiguration in ASP.NET Core

> [!NOTE]
> Dieses Thema gilt für Blazor WebAssembly. Eine allgemeine Anleitung zur App-Konfiguration in ASP.NET Core finden Sie unter <xref:fundamentals/configuration/index>.

Blazor WebAssembly lädt die Konfiguration standardmäßig aus den folgenden App-Einstellungsdateien:

* `wwwroot/appsettings.json`.
* `wwwroot/appsettings.{ENVIRONMENT}.json`, wobei der Platzhalter `{ENVIRONMENT}` die [Laufzeitumgebung](xref:fundamentals/environments) der App ist.

Andere in der App registrierte Konfigurationsanbieter können auch Konfigurationen bereitstellen, aber nicht alle Anbieter und Anbieterfunktionen eignen sich für Blazor WebAssembly-Apps:

* [Azure Key Vault-Konfigurationsanbieter](xref:security/key-vault-configuration): Der Anbieter wird für die verwaltete Identität und die Anwendungs-ID-Szenarien (Client-ID) mit geheimem Clientschlüssel nicht unterstützt. Die Anwendungs-ID mit einem geheimen Clientschlüssel wird nicht für ASP.NET Core-Apps empfohlen, insbesondere nicht für Blazor WebAssembly-Apps, da der geheime Clientschlüssel nicht clientseitig für den Zugriff auf den Azure Key Vault-Dienst geschützt werden kann.
* [Azure-App-Konfigurationsanbieter](/azure/azure-app-configuration/quickstart-aspnet-core-app): Der Anbieter eignet sich nicht für Blazor WebAssembly-Apps, da Blazor WebAssembly-Apps nicht auf einem Server in Azure ausgeführt werden.

> [!WARNING]
> Die Konfiguration in einer Blazor WebAssembly-App ist für Benutzer sichtbar. **Speichern Sie keine App-Geheimnisse, Anmeldeinformationen oder andere vertrauliche Daten in der Konfiguration einer Blazor WebAssembly-App.**

Weitere Informationen zur Konfigurationsanbietern finden Sie unter <xref:fundamentals/configuration/index>.

## <a name="app-settings-configuration"></a>Konfiguration von App-Einstellungen

Die Konfiguration in App-Einstellungsdateien wird standardmäßig geladen. Im folgenden Beispiel wird ein Benutzeroberflächen-Konfigurationswert in einer App-Einstellungsdatei gespeichert und automatisch vom Blazor-Framework geladen. Der Wert wird von einer Komponente gelesen.

`wwwroot/appsettings.json`:

```json
{
  "h1FontSize": "50px"
}
```

Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen.

`Pages/ConfigurationExample.razor`:

```razor
@page "/configuration-example"
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration

<h1 style="font-size:@Configuration["h1FontSize"]">
    Configuration example
</h1>
```

Um andere Konfigurationsdateien aus dem Ordner `wwwroot` in die Konfiguration einzulesen, verwenden Sie einen <xref:System.Net.Http.HttpClient>, um den Inhalt der Datei abzurufen. Im folgenden Beispiel wird eine Konfigurationsdatei (`cars.json`) in die Konfiguration der App gelesen.

`wwwroot/cars.json`:

```json
{
    "size": "tiny"
}
```

Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.Configuration?displayProperty=fullName> hinzu:

```csharp
using Microsoft.Extensions.Configuration;
```

Ändern Sie in `Program.Main` von `Program.cs` die vorhandene <xref:System.Net.Http.HttpClient>-Dienstregistrierung so, dass der Client zum Lesen der Datei verwendet wird:

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

## <a name="custom-configuration-provider-with-ef-core"></a>Benutzerdefinierter Konfigurationsanbieter mit EF Core

Der benutzerdefinierte Konfigurationsanbieter mit EF Core, der in <xref:fundamentals/configuration/index#custom-configuration-provider> gezeigt wird, funktioniert mit Blazor WebAssembly-Apps.

> [!WARNING]
> Datenbank-Verbindungszeichenfolgen und Datenbanken, die mit Blazor WebAssembly-Apps geladen werden, sind nicht sicher und sollten nicht zum Speichern vertraulicher Daten verwendet werden.

Fügen Sie der Projektdatei der App Paketverweise für [`Microsoft.EntityFrameworkCore`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) und [`Microsoft.EntityFrameworkCore.InMemory`](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory) hinzu.

Fügen Sie die Entity Framework Core-Konfigurationsklassen hinzu, die unter <xref:fundamentals/configuration/index#custom-configuration-provider> beschrieben werden.

Fügen Sie `Program.cs` die Namespaces für <xref:Microsoft.EntityFrameworkCore?displayProperty=fullName> und <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> hinzu:

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration.Memory;
```

In `Program.Main` von `Program.cs`:

```csharp
builder.Configuration.AddEFConfiguration(
    options => options.UseInMemoryDatabase("InMemoryDb"));
```

Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen.

`Pages/EFCoreConfig.razor`:

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

## <a name="memory-configuration-source"></a>Arbeitsspeicher als Konfigurationsquelle

Im folgenden Beispiel wird ein <xref:Microsoft.Extensions.Configuration.Memory.MemoryConfigurationSource>-Element in `Program.Main` verwendet, um eine zusätzliche Konfiguration bereitzustellen.

Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.Configuration.Memory?displayProperty=fullName> hinzu:

```csharp
using Microsoft.Extensions.Configuration.Memory;
```

In `Program.Main` von `Program.cs`:

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

Fügen Sie eine <xref:Microsoft.Extensions.Configuration.IConfiguration>-Instanz in eine Komponente ein, um auf die Konfigurationsdaten zuzugreifen.

`Pages/MemoryConfig.razor`:

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

Rufen Sie einen Abschnitt der Konfiguration in C#-Code mit <xref:Microsoft.Extensions.Configuration.IConfiguration.GetSection%2A?displayProperty=nameWithType> ab. Im folgenden Beispiel wird der `wheels`-Abschnitt für die Konfiguration im vorherigen Beispiel abgerufen:

```razor
@code {
    protected override void OnInitialized()
    {
        var wheelsSection = Configuration.GetSection("wheels");

        ...
    }
}
```

## <a name="authentication-configuration"></a>Authentifizierungskonfiguration

Geben Sie die Authentifizierungskonfiguration in einer App-Einstellungsdatei an.

`wwwroot/appsettings.json`:

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

Laden Sie die Konfiguration für einen Identity-Anbieter mit <xref:Microsoft.Extensions.Configuration.ConfigurationBinder.Bind%2A?displayProperty=nameWithType> in `Program.Main`. Im folgenden Beispiel wird die Konfiguration für einen [OIDC-Anbieter](xref:blazor/security/webassembly/standalone-with-authentication-library) geladen.

`Program.cs`:

```csharp
builder.Services.AddOidcAuthentication(options =>
    builder.Configuration.Bind("Local", options.ProviderOptions));
```

## <a name="logging-configuration"></a>Konfiguration der Protokollierung

Fügen Sie einen Paketverweis für [`Microsoft.Extensions.Logging.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Configuration) in der Projektdatei der App hinzu. Geben Sie in der App-Einstellungsdatei die Protokollierungskonfiguration an. Die Protokollierungskonfiguration wird in `Program.Main` geladen.

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

Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.Extensions.Logging?displayProperty=fullName> hinzu:

```csharp
using Microsoft.Extensions.Logging;
```

In `Program.Main` von `Program.cs`:

```csharp
builder.Logging.AddConfiguration(
    builder.Configuration.GetSection("Logging"));
```

## <a name="host-builder-configuration"></a>Konfiguration des Host-Generators

Lesen Sie die Host-Generatorkonfiguration aus <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Configuration?displayProperty=nameWithType> in `Program.Main`.

In `Program.Main` von `Program.cs`:

```csharp
var hostname = builder.Configuration["HostName"];
```

## <a name="cached-configuration"></a>Zwischengespeicherte Konfiguration

Konfigurationsdateien werden zur Offlineverwendung zwischengespeichert. Bei [progressiven Web-Apps (PWAs)](xref:blazor/progressive-web-app) können Sie Konfigurationsdateien nur beim Erstellen einer neuen Bereitstellung aktualisieren. Die Bearbeitung von Konfigurationsdateien zwischen Bereitstellungen hat aus folgenden Gründen keine Auswirkungen:

* Benutzer verfügen über zwischengespeicherte Versionen der Dateien, die sie weiterhin verwenden können.
* Die Dateien `service-worker.js` und `service-worker-assets.js` der PWA müssen bei der Kompilierung neu erstellt werden, wodurch der App beim nächsten Onlineaufruf des Benutzers signalisiert wird, dass die App neu bereitgestellt wurde.

Weitere Informationen zur Verarbeitung von Updates im Hintergrund durch PWAs finden Sie unter <xref:blazor/progressive-web-app#background-updates>.
