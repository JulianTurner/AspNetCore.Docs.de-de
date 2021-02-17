---
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
ms.openlocfilehash: 087c5a7dd20bc653a05c337dde905716032e00b3
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551972"
---
## <a name="debug-diagnostics"></a><span data-ttu-id="d7ef0-101">Debugdiagnose</span><span class="sxs-lookup"><span data-stu-id="d7ef0-101">Debug diagnostics</span></span>

<span data-ttu-id="d7ef0-102">Legen Sie für eine ausführliche Routingdiagnoseausgabe `Logging:LogLevel:Microsoft` auf `Debug` fest.</span><span class="sxs-lookup"><span data-stu-id="d7ef0-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="d7ef0-103">Legen Sie in der Entwicklungsumgebung die Protokollebene in *appsettings.Development.json* fest:</span><span class="sxs-lookup"><span data-stu-id="d7ef0-103">In the development environment, set the log level in *appsettings.Development.json*:</span></span>

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}
```
