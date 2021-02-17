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
ms.openlocfilehash: ddc6e2abf60577644a38b0b6ad0c74a734205ef5
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552719"
---
<span data-ttu-id="e94e9-101">Die Middleware für weitergeleitete Header muss noch vor einer anderen Middleware ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e94e9-101">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="e94e9-102">Mit dieser Reihenfolge wird sichergestellt, dass die auf Informationen von weitergeleiteten Headern basierende Middleware die zu verarbeitenden Headerwerte nutzen kann.</span><span class="sxs-lookup"><span data-stu-id="e94e9-102">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="e94e9-103">Unter [Middleware für weitergeleitete Header: Auftrag](xref:host-and-deploy/proxy-load-balancer#fhmo) finden Sie Informationen zum Ausführen der Middleware für weitergeleitete Header nach der diagnostischen Middleware und der Middleware für die Fehlerbehandlung.</span><span class="sxs-lookup"><span data-stu-id="e94e9-103">To run Forwarded Headers Middleware after diagnostics and error handling middleware, see [Forwarded Headers Middleware order](xref:host-and-deploy/proxy-load-balancer#fhmo).</span></span>