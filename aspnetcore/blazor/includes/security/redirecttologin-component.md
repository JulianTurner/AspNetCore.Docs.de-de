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
ms.openlocfilehash: 9b7b302485a5c9ab7d1b1da6ce4d8ab7d1c26f9c
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551334"
---
<span data-ttu-id="7a0ed-101">Die `RedirectToLogin`-Komponente (`Shared/RedirectToLogin.razor`):</span><span class="sxs-lookup"><span data-stu-id="7a0ed-101">The `RedirectToLogin` component (`Shared/RedirectToLogin.razor`):</span></span>

* <span data-ttu-id="7a0ed-102">Verwaltet die Umleitung nicht autorisierter Benutzer auf die Anmeldeseite.</span><span class="sxs-lookup"><span data-stu-id="7a0ed-102">Manages redirecting unauthorized users to the login page.</span></span>
* <span data-ttu-id="7a0ed-103">Behält die aktuelle URL bei, auf die der Benutzer zuzugreifen versucht, sodass er bei erfolgreicher Authentifizierung zu dieser Seite zurückkehren kann.</span><span class="sxs-lookup"><span data-stu-id="7a0ed-103">Preserves the current URL that the user is attempting to access so that they can be returned to that page if authentication is successful.</span></span>

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
