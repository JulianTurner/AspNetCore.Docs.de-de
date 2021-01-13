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
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="b5918-103">ASP.NET Core Blazor-Protokollierung</span><span class="sxs-lookup"><span data-stu-id="b5918-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="b5918-104">Konfigurieren Sie die benutzerdefinierte Protokollierung in Blazor WebAssembly-Apps mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType>-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="b5918-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="b5918-105">Fügen Sie `Program.cs` den Namespace für <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="b5918-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="b5918-106">Legen Sie in `Program.Main` von `Program.cs` den Mindestprotokolliergrad mit <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> fest, und fügen Sie den benutzerdefinierten Protokollierungsanbieter hinzu:</span><span class="sxs-lookup"><span data-stu-id="b5918-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="b5918-107">Die `Logging`-Eigenschaft ist vom Typ <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, deshalb sind alle auf <xref:Microsoft.Extensions.Logging.ILoggingBuilder> verfügbaren Erweiterungsmethoden auch auf `Logging` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="b5918-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="b5918-108">Die Protokollierungskonfiguration kann aus App-Einstellungsdateien geladen werden.</span><span class="sxs-lookup"><span data-stu-id="b5918-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="b5918-109">Weitere Informationen finden Sie unter <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="b5918-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="no-locsignalr-net-client-logging"></a><span data-ttu-id="b5918-110">SignalR .NET-Clientprotokollierung</span><span class="sxs-lookup"><span data-stu-id="b5918-110">SignalR .NET client logging</span></span>

<span data-ttu-id="b5918-111">Fügen Sie eine <xref:Microsoft.Extensions.Logging.ILoggerProvider>-Instanz ein, um `WebAssemblyConsoleLogger` zu den an <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> weitergegebenen Protokollierungsanbietern hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b5918-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="b5918-112">Anders als bei einem herkömmlichen <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> handelt es sich bei `WebAssemblyConsoleLogger` um einen Wrapper um browserspezifische Protokollierungs-APIs (z. B. `console.log`).</span><span class="sxs-lookup"><span data-stu-id="b5918-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="b5918-113">Die Verwendung von `WebAssemblyConsoleLogger` ermöglicht die Protokollierung in Mono in einem Browserkontext.</span><span class="sxs-lookup"><span data-stu-id="b5918-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

> [!NOTE]
> <span data-ttu-id="b5918-114">`WebAssemblyConsoleLogger` ist [intern](/dotnet/csharp/language-reference/keywords/internal) und nicht für die direkte Verwendung in Entwicklercode verfügbar.</span><span class="sxs-lookup"><span data-stu-id="b5918-114">`WebAssemblyConsoleLogger` is [internal](/dotnet/csharp/language-reference/keywords/internal) and not available for direct use in developer code.</span></span>

<span data-ttu-id="b5918-115">Fügen Sie den Namespace für <xref:Microsoft.Extensions.Logging?displayProperty=fullName> hinzu, und fügen Sie <xref:Microsoft.Extensions.Logging.ILoggerProvider> in die Komponente ein:</span><span class="sxs-lookup"><span data-stu-id="b5918-115">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="b5918-116">Verwenden Sie <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> in der [`OnInitializedAsync`-Methode](xref:blazor/components/lifecycle#component-initialization-methods) der Komponente:</span><span class="sxs-lookup"><span data-stu-id="b5918-116">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="b5918-117">Allgemeine ASP.NET Core-Protokollierungsanleitungen zu Blazor Server finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="b5918-117">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="b5918-118">Protokoll in Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="b5918-118">Log in Razor components</span></span>

<span data-ttu-id="b5918-119">Protokollierungen beachten die Startkonfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="b5918-119">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="b5918-120">Die `using`-Anweisung für <xref:Microsoft.Extensions.Logging> ist erforderlich, um die [IntelliSense](/visualstudio/ide/using-intellisense)-Vervollständigungen für APIs zu unterstützen, zum Beispiel <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> und <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="b5918-120">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="b5918-121">Im folgenden Beispiel wird die Protokollierung mit <xref:Microsoft.Extensions.Logging.ILogger> in Komponenten veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="b5918-121">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="b5918-122">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5918-122">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

<span data-ttu-id="b5918-123">Im folgenden Beispiel wird die Protokollierung mit <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Komponenten veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="b5918-123">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="b5918-124">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="b5918-124">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a><span data-ttu-id="b5918-125">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b5918-125">Additional resources</span></span>

* <xref:fundamentals/logging/index>
