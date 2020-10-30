---
title: Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0
author: pakrym
description: Erfahren Sie, wie Sie eine Non-ASP.net Core-Anwendung, die Microsoft. Extensions. Logging verwendet, von 2,1 zu 2,2 oder 3,0 migrieren.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
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
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 1082ae494aae7a31b195b32dfedae81c60fe3eed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060377"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="8e072-103">Migrieren von Microsoft. Extensions. Logging 2,1 zu 2,2 oder 3,0</span><span class="sxs-lookup"><span data-stu-id="8e072-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="8e072-104">In diesem Artikel werden die allgemeinen Schritte zum Migrieren einer Non-ASP.net Core-Anwendung beschrieben, die `Microsoft.Extensions.Logging` zwischen 2,1 und 2,2 oder 3,0 verwendet.</span><span class="sxs-lookup"><span data-stu-id="8e072-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="8e072-105">2.1 zu 2.2</span><span class="sxs-lookup"><span data-stu-id="8e072-105">2.1 to 2.2</span></span>

<span data-ttu-id="8e072-106">Erstellen Sie manuell `ServiceCollection` und rufen Sie auf `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="8e072-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="8e072-107">2,1 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8e072-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="8e072-108">2,2 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8e072-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="8e072-109">2,1 bis 3,0</span><span class="sxs-lookup"><span data-stu-id="8e072-109">2.1 to 3.0</span></span>

<span data-ttu-id="8e072-110">Verwenden Sie in 3,0 `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="8e072-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="8e072-111">2,1 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8e072-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="8e072-112">3,0 Beispiel:</span><span class="sxs-lookup"><span data-stu-id="8e072-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="8e072-113">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8e072-113">Additional resources</span></span>

* <span data-ttu-id="8e072-114">Das [nuget-Paket Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="8e072-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
