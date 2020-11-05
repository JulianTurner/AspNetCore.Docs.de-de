---
title: ASP.NET Core :::no-loc(Blazor):::-Protokollierung
author: guardrex
description: Erfahren Sie mehr über die Protokollierung in :::no-loc(Blazor):::-Apps, einschließlich der Konfiguration auf Protokollebene und des Schreibens von Protokollmeldungen von :::no-loc(Razor):::-Komponenten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
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
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055983"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="9ea40-103">ASP.NET Core :::no-loc(Blazor):::-Protokollierung</span><span class="sxs-lookup"><span data-stu-id="9ea40-103">ASP.NET Core :::no-loc(Blazor)::: logging</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="9ea40-104">Konfigurieren Sie die Protokollierung in :::no-loc(Blazor WebAssembly):::-Apps mit der `WebAssemblyHostBuilder.Logging`-Eigenschaft in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="9ea40-104">Configure logging in :::no-loc(Blazor WebAssembly)::: apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="9ea40-105">Die `Logging`-Eigenschaft ist vom Typ <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, deshalb sind alle auf <xref:Microsoft.Extensions.Logging.ILoggingBuilder> verfügbaren Erweiterungsmethoden auch auf `Logging` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="9ea40-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="9ea40-106">Die Protokollierungskonfiguration kann aus App-Einstellungsdateien geladen werden.</span><span class="sxs-lookup"><span data-stu-id="9ea40-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="9ea40-107">Weitere Informationen finden Sie unter <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="9ea40-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## :::no-loc(Blazor Server):::

<span data-ttu-id="9ea40-108">Einen allgemeinen Leitfaden zur Protokollierung in ASP.NET Core finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="9ea40-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="9ea40-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET-Clientprotokollierung</span><span class="sxs-lookup"><span data-stu-id="9ea40-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET client logging</span></span>

<span data-ttu-id="9ea40-110">Fügen Sie eine <xref:Microsoft.Extensions.Logging.ILoggerProvider>-Instanz ein, um `WebAssemblyConsoleLogger` zu den an <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> weitergegebenen Protokollierungsanbietern hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="9ea40-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="9ea40-111">Anders als bei einem herkömmlichen <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> handelt es sich bei `WebAssemblyConsoleLogger` um einen Wrapper um browserspezifische Protokollierungs-APIs (z. B. `console.log`).</span><span class="sxs-lookup"><span data-stu-id="9ea40-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="9ea40-112">Die Verwendung von `WebAssemblyConsoleLogger` ermöglicht die Protokollierung in Mono in einem Browserkontext.</span><span class="sxs-lookup"><span data-stu-id="9ea40-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="9ea40-113">Protokoll in :::no-loc(Razor):::-Komponenten</span><span class="sxs-lookup"><span data-stu-id="9ea40-113">Log in :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="9ea40-114">Protokollierungen beachten die Startkonfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="9ea40-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="9ea40-115">Die `using`-Direktive für <xref:Microsoft.Extensions.Logging> ist erforderlich, um die IntelliSense-Vervollständigungen für APIs zu unterstützen, zum Beispiel <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> und <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="9ea40-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="9ea40-116">Im folgenden Beispiel wird die Protokollierung mit <xref:Microsoft.Extensions.Logging.ILogger> in :::no-loc(Razor):::-Komponenten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="9ea40-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="9ea40-117">Im folgenden Beispiel wird die Protokollierung mit <xref:Microsoft.Extensions.Logging.ILoggerFactory> in :::no-loc(Razor):::-Komponenten veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="9ea40-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in :::no-loc(Razor)::: components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="9ea40-118">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9ea40-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
