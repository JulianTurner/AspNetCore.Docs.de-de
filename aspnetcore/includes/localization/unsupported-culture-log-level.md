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
ms.openlocfilehash: 7caea4089d3624d4c02db4b8adbe9edb73f3d31a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552641"
---
> [!NOTE]
> <span data-ttu-id="71857-101">Web-Apps vor ASP.NET Core 3.0 schreiben ein Protokoll vom Typ `LogLevel.Warning` pro Anforderung, wenn die angeforderte Kultur nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="71857-101">Prior to ASP.NET Core 3.0 web apps write one log of type `LogLevel.Warning` per request if the requested culture is unsupported.</span></span> <span data-ttu-id="71857-102">Durch die Protokollierung einer `LogLevel.Warning` pro Anforderung können große Protokolldateien mit redundanten Informationen entstehen.</span><span class="sxs-lookup"><span data-stu-id="71857-102">Logging one `LogLevel.Warning` per request is can make large log files with redundant information.</span></span> <span data-ttu-id="71857-103">Dieses Verhalten wurde in ASP.NET 3.0 geändert.</span><span class="sxs-lookup"><span data-stu-id="71857-103">This behavior has been changed in ASP.NET 3.0.</span></span> <span data-ttu-id="71857-104">Die `RequestLocalizationMiddleware` schreibt ein Protokoll vom Typ `LogLevel.Debug`, wodurch die Größe der Produktionsprotokolle reduziert wird.</span><span class="sxs-lookup"><span data-stu-id="71857-104">The `RequestLocalizationMiddleware` writes a log of type `LogLevel.Debug`, which reduces the size of production logs.</span></span>
