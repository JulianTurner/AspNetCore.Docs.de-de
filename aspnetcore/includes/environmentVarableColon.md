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
ms.openlocfilehash: 6435d39b6d442f0d4643d77d9111d11b50781544
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536291"
---
<span data-ttu-id="c7f8c-101">Das Trennzeichen `:` funktioniert nicht auf allen Plattformen mit den hierarchischen Schlüsseln von Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="c7f8c-101">The `:` separator doesn't work with environment variable hierarchical keys on all platforms.</span></span> <span data-ttu-id="c7f8c-102">Der doppelte Unterstrich `__`:</span><span class="sxs-lookup"><span data-stu-id="c7f8c-102">`__`, the double underscore, is:</span></span>

* <span data-ttu-id="c7f8c-103">wird auf allen Plattformen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c7f8c-103">Supported by all platforms.</span></span> <span data-ttu-id="c7f8c-104">Das Trennzeichen `:` wird beispielsweise nicht von [Bash](https://linuxhint.com/bash-environment-variables/) unterstützt, `__` hingegen schon.</span><span class="sxs-lookup"><span data-stu-id="c7f8c-104">For example, the `:` separator is not supported by [Bash](https://linuxhint.com/bash-environment-variables/), but `__` is.</span></span>
* <span data-ttu-id="c7f8c-105">automatisch durch `:` ersetzt.</span><span class="sxs-lookup"><span data-stu-id="c7f8c-105">Automatically replaced by a `:`</span></span>