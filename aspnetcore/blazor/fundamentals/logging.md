---
title: ASP.NET Core Blazor-Protokollierung
author: guardrex
description: Erfahren Sie mehr über die Protokollierung in Blazor-Apps, einschließlich der Konfiguration auf Protokollebene und des Schreibens von Protokollmeldungen von Razor-Komponenten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/16/2020
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
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 10c96bd2d0cc64f3bd035e7079b0996eb5768595
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97666832"
---
# <a name="aspnet-core-no-locblazor-logging"></a>ASP.NET Core Blazor-Protokollierung

::: zone pivot="webassembly"

Konfigurieren Sie die benutzerdefinierte Protokollierung in Blazor WebAssembly-Apps mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType>-Eigenschaft.

Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> hinzu:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

Legen Sie in `Program.Main` von `Program.cs` den Mindestprotokolliergrad mit <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> fest, und fügen Sie den benutzerdefinierten Protokollierungsanbieter hinzu:

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

Die `Logging`-Eigenschaft ist vom Typ <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, deshalb sind alle auf <xref:Microsoft.Extensions.Logging.ILoggingBuilder> verfügbaren Erweiterungsmethoden auch auf `Logging` verfügbar.

Die Protokollierungskonfiguration kann aus App-Einstellungsdateien geladen werden. Weitere Informationen finden Sie unter <xref:blazor/fundamentals/configuration#logging-configuration>.

## <a name="no-locsignalr-net-client-logging"></a>SignalR .NET-Clientprotokollierung

Fügen Sie eine <xref:Microsoft.Extensions.Logging.ILoggerProvider>-Instanz ein, um `WebAssemblyConsoleLogger` zu den an <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> weitergegebenen Protokollierungsanbietern hinzuzufügen. Anders als bei einem herkömmlichen <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> handelt es sich bei `WebAssemblyConsoleLogger` um einen Wrapper um browserspezifische Protokollierungs-APIs (z. B. `console.log`). Die Verwendung von `WebAssemblyConsoleLogger` ermöglicht die Protokollierung in Mono in einem Browserkontext.

> [!NOTE]
> `WebAssemblyConsoleLogger` ist [intern](/dotnet/csharp/language-reference/keywords/internal) und nicht für die direkte Verwendung in Entwicklercode verfügbar.

Fügen Sie den Namespace für <xref:Microsoft.Extensions.Logging?displayProperty=fullName> hinzu, und fügen Sie <xref:Microsoft.Extensions.Logging.ILoggerProvider> in die Komponente ein:

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

Verwenden Sie <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> in der [`OnInitializedAsync`-Methode](xref:blazor/components/lifecycle#component-initialization-methods) der Komponente:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

Allgemeine ASP.NET Core-Protokollierungsanleitungen zu Blazor Server finden Sie unter <xref:fundamentals/logging/index>.

::: zone-end

## <a name="log-in-no-locrazor-components"></a>Protokoll in Razor-Komponenten

Protokollierungen beachten die Startkonfiguration der App.

Die `using`-Anweisung für <xref:Microsoft.Extensions.Logging> ist erforderlich, um die [IntelliSense](/visualstudio/ide/using-intellisense)-Vervollständigungen für APIs zu unterstützen, zum Beispiel <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> und <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.

Im folgenden Beispiel wird die Protokollierung mit <xref:Microsoft.Extensions.Logging.ILogger> in Komponenten veranschaulicht.

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

Im folgenden Beispiel wird die Protokollierung mit <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Komponenten veranschaulicht.

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/logging/index>
