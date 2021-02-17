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
ms.openlocfilehash: 30c4c469453f0202fe5310dbe00b3d7214f49b5a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552269"
---
* `-F|--no-formatting`

  <span data-ttu-id="578c5-101">Ein Flag, das die Unterdrückung der HTTP-Antwortformatierung bewirkt.</span><span class="sxs-lookup"><span data-stu-id="578c5-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="578c5-102">Legt einen HTTP-Anforderungsheader fest.</span><span class="sxs-lookup"><span data-stu-id="578c5-102">Sets an HTTP request header.</span></span> <span data-ttu-id="578c5-103">Die folgenden zwei Werteformate werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="578c5-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  <span data-ttu-id="578c5-104">Gibt eine Datei an, in die der HTTP-Antworttext geschrieben werden soll.</span><span class="sxs-lookup"><span data-stu-id="578c5-104">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="578c5-105">Beispiel: `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="578c5-105">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="578c5-106">Wenn die Datei nicht vorhanden ist, wird sie erstellt.</span><span class="sxs-lookup"><span data-stu-id="578c5-106">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="578c5-107">Gibt eine Datei an, in die die HTTP-Antwortheader geschrieben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="578c5-107">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="578c5-108">Beispiel: `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="578c5-108">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="578c5-109">Wenn die Datei nicht vorhanden ist, wird sie erstellt.</span><span class="sxs-lookup"><span data-stu-id="578c5-109">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="578c5-110">Ein Flag, das das Streaming der HTTP-Antwort aktiviert.</span><span class="sxs-lookup"><span data-stu-id="578c5-110">A flag whose presence enables streaming of the HTTP response.</span></span>
