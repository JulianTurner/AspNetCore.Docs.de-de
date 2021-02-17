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
ms.openlocfilehash: c6880852f63b1e91789667506f01680608933226
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552873"
---
> [!WARNING]
> <span data-ttu-id="1dcc0-101">Aus Sicherheitsgründen müssen Sie Daten von `GET`-Anforderungen in die Seitenmodelleigenschaften einbinden.</span><span class="sxs-lookup"><span data-stu-id="1dcc0-101">For security reasons, you must opt in to binding `GET` request data to page model properties.</span></span> <span data-ttu-id="1dcc0-102">Überprüfen Sie die Benutzereingaben, bevor Sie sie den Eigenschaften zuordnen.</span><span class="sxs-lookup"><span data-stu-id="1dcc0-102">Verify user input before mapping it to properties.</span></span> <span data-ttu-id="1dcc0-103">Die Verwendung der `GET`-Bindung ist von Vorteil, wenn Sie Szenarios behandeln, die von Abfragezeichenfolgen oder Routenwerten abhängig sind.</span><span class="sxs-lookup"><span data-stu-id="1dcc0-103">Opting into `GET` binding is useful when addressing scenarios that rely on query string or route values.</span></span>
>
> <span data-ttu-id="1dcc0-104">Legen Sie die `SupportsGet`-Eigenschaft des [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute)-Attributs auf `true` fest, um eine Eigenschaft an `GET`-Anforderungen zu binden:</span><span class="sxs-lookup"><span data-stu-id="1dcc0-104">To bind a property on `GET` requests, set the [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute's `SupportsGet` property to `true`:</span></span>
>
> ```csharp
> [BindProperty(SupportsGet = true)]
> ```
>
> <span data-ttu-id="1dcc0-105">Weitere Informationen finden Sie im Video [ASP.NET Core Community Standup: Bind on GET discussion (YouTube) (ASP.NET Core Community Standup: Diskussion zur Bindung an GET)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span><span class="sxs-lookup"><span data-stu-id="1dcc0-105">For more information, see [ASP.NET Core Community Standup: Bind on GET discussion (YouTube)](https://www.youtube.com/watch?v=p7iHB9V-KVU&feature=youtu.be&t=54m27s).</span></span>
