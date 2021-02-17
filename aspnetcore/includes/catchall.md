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
ms.openlocfilehash: ca743cdb39423d833902c330f6f0682b600c9833
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551322"
---
::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

> [!WARNING]
> <span data-ttu-id="02b45-101">Ein **catch-all**-Parameter kann aufgrund eines [Fehlers](https://github.com/dotnet/aspnetcore/issues/18677) beim Routing nicht ordnungsgemäß mit Routen übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="02b45-101">A **catch-all** parameter may match routes incorrectly due to a [bug](https://github.com/dotnet/aspnetcore/issues/18677) in routing.</span></span> <span data-ttu-id="02b45-102">Apps, die von diesem Fehler betroffen sind, weisen die folgenden Merkmale auf:</span><span class="sxs-lookup"><span data-stu-id="02b45-102">Apps impacted by this bug have the following characteristics:</span></span>
>
> * <span data-ttu-id="02b45-103">Eine catch-all-Route, zum Beispiel `{**slug}"`</span><span class="sxs-lookup"><span data-stu-id="02b45-103">A catch-all route, for example, `{**slug}"`</span></span>
> * <span data-ttu-id="02b45-104">Die catch-all-Route kann nicht mit Anforderungen abgeglichen werden, die abgeglichen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="02b45-104">The catch-all route fails to match requests it should match.</span></span>
> * <span data-ttu-id="02b45-105">Durch das Entfernen anderer Routen funktioniert die catch-all-Route.</span><span class="sxs-lookup"><span data-stu-id="02b45-105">Removing other routes makes catch-all route start working.</span></span>
>
> <span data-ttu-id="02b45-106">Weitere Beispiele zu diesem Fehler finden Sie in den GitHub-Issues [18677](https://github.com/dotnet/aspnetcore/issues/18677) und [16579](https://github.com/dotnet/aspnetcore/issues/16579).</span><span class="sxs-lookup"><span data-stu-id="02b45-106">See GitHub bugs [18677](https://github.com/dotnet/aspnetcore/issues/18677) and [16579](https://github.com/dotnet/aspnetcore/issues/16579) for example cases that hit this bug.</span></span>
>
> <span data-ttu-id="02b45-107">Eine Opt-in-Behebung für diesen Fehler ist im [.NET Core 3.1.301 SDK und höher](https://dotnet.microsoft.com/download/dotnet-core/3.1) enthalten.</span><span class="sxs-lookup"><span data-stu-id="02b45-107">An opt-in fix for this bug is contained in [.NET Core 3.1.301 SDK and later](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="02b45-108">Der folgende Code legt einen internen Switch fest, mit dem dieser Fehler behoben wird:</span><span class="sxs-lookup"><span data-stu-id="02b45-108">The following code sets an internal switch that fixes this bug:</span></span>
>
>```csharp
>public static void Main(string[] args)
>{
>    AppContext.SetSwitch("Microsoft.AspNetCore.Routing.UseCorrectCatchAllBehavior", 
>                          true);
>    CreateHostBuilder(args).Build().Run();
>}
>// Remaining code removed for brevity.
>```

::: moniker-end