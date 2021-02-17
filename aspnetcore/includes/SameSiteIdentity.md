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
ms.openlocfilehash: b6f6bc2e094c9070e0ea57b507f558313f19bc15
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551863"
---
<span data-ttu-id="e11a6-101">ASP.net Core [Identity](xref:security/authentication/identity) ist von [SameSite cookie s](xref:security/samesite) größtenteils nicht betroffen, ausgenommen Erweiterte Szenarien wie `IFrames` oder die `OpenIdConnect` Integration.</span><span class="sxs-lookup"><span data-stu-id="e11a6-101">ASP.NET Core [Identity](xref:security/authentication/identity) is largely unaffected by [SameSite cookies](xref:security/samesite) except for advanced scenarios like `IFrames` or `OpenIdConnect` integration.</span></span>

<span data-ttu-id="e11a6-102">Wenn Sie verwenden `Identity` , ***können Sie*** keine cookie Anbieter oder Aufrufe hinzufügen ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)` `Identity` .</span><span class="sxs-lookup"><span data-stu-id="e11a6-102">When using `Identity`, do ***not*** add any cookie providers or call ` services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)`, `Identity` takes care of that.</span></span>