---
title: ASP.NET Core Blazor-Umgebungen
author: guardrex
description: Weitere Informationen zu Umgebungen und zum Festlegen der Umgebung einer Blazor WebAssembly-App finden Sie unter Blazor.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
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
uid: blazor/fundamentals/environments
ms.openlocfilehash: a5ead59e467da331b585e8daefb1d7d259c7edba
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101047"
---
# <a name="aspnet-core-blazor-environments"></a>ASP.NET Core Blazor-Umgebungen

> [!NOTE]
> Dieses Thema gilt für Blazor WebAssembly. Eine allgemeine Anleitung zur Konfiguration von ASP.NET Core-Apps, in der die Ansätze für Blazor Server-Apps beschrieben werden, finden Sie unter <xref:fundamentals/environments>.

Wenn eine App lokal ausgeführt wird, wird Umgebung standardmäßig auf „Entwicklung“ festgelegt. Wenn die App veröffentlicht wird, wird standardmäßig die Produktionsumgebung verwendet.

Die clientseitige Blazor-App ( *`Client`* ) einer gehosteten Blazor WebAssembly-Lösung bestimmt die Umgebung aus der *`Server`* -App der Lösung über eine Middleware, die dem Browser die Umgebung mitteilt. Die *`Server`* -App fügt einen Header namens `blazor-environment` hinzu, wobei die Umgebung den Wert des Headers darstellt. Der *`Client`* -App liest den Header. Die *`Server`* -App der Lösung ist eine ASP.NET Core-App. Weitere Informationen zum Konfigurieren der Umgebung finden Sie unter <xref:fundamentals/environments>.

Wenn eine eigenständige Blazor WebAssembly-App lokal ausgeführt wird, fügt der Entwicklungsserver den `blazor-environment`-Header hinzu, um die Entwicklungsumgebung anzugeben. Um die Umgebung für andere Hostingumgebungen anzugeben, fügen Sie den `blazor-environment`-Header hinzu.

Im folgenden Beispiel für IIS wird der benutzerdefinierte Header (`blazor-environment`) zu der veröffentlichten `web.config`-Datei hinzugefügt. Die `web.config`-Datei befindet sich im Ordner `bin/Release/{TARGET FRAMEWORK}/publish`, wobei der Platzhalter `{TARGET FRAMEWORK}` für das Zielframework steht:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>

    ...

    <httpProtocol>
      <customHeaders>
        <add name="blazor-environment" value="Staging" />
      </customHeaders>
    </httpProtocol>
  </system.webServer>
</configuration>
```

> [!NOTE]
> Informationen zum Verwenden einer benutzerdefinierten Datei `web.config` für IIS, die nicht überschrieben wird, wenn die App im Ordner `publish` veröffentlicht wird, finden Sie unter <xref:blazor/host-and-deploy/webassembly#use-a-custom-webconfig>.

Rufen Sie die Umgebung der App in einer Komponente ab, indem Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> einfügen und die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.Environment>-Eigenschaft lesen.

`Pages/ReadEnvironment.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/environments/ReadEnvironment.razor?highlight=3,7)]

::: moniker-end

Während des Starts macht <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder> die Schnittstelle <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment> über die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.HostEnvironment> -Eigenschaft verfügbar, die umgebungsspezifische Logik im Code des Host-Generators ermöglicht.

In `Program.Main` von `Program.cs`:

```csharp
if (builder.HostEnvironment.Environment == "Custom")
{
    ...
};
```

Die folgenden bequemen, von <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions> bereitgestellten Erweiterungsmethoden ermöglichen die Überprüfung der aktuellen Umgebung auf die Namen „Development“, „Production“, „Staging“ sowie auf benutzerdefinierte Umgebungsnamen:

* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsDevelopment%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsProduction%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsStaging%2A>
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostEnvironmentExtensions.IsEnvironment%2A>

In `Program.Main` von `Program.cs`:

```csharp
if (builder.HostEnvironment.IsStaging())
{
    ...
};

if (builder.HostEnvironment.IsEnvironment("Custom"))
{
    ...
};
```

Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType>-Eigenschaft kann während des Starts verwendet werden, wenn der <xref:Microsoft.AspNetCore.Components.NavigationManager>-Dienst nicht verfügbar ist.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/environments>
