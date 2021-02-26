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
ms.openlocfilehash: d5e1630d6a9e412c40831aa3a66aaed7524ff501
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552075"
---
<span data-ttu-id="6d5db-101">HTTP-Nachspanne ähneln den HTTP-Headers, jedoch werden sie erst gesendet, nachdem der Antworttext gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="6d5db-101">HTTP Trailers are similar to HTTP Headers, except they are sent after the response body is sent.</span></span> <span data-ttu-id="6d5db-102">Für IIS und HTTP.sys werden nur HTTP/2-Antwortnachspanne unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6d5db-102">For IIS and HTTP.sys, only HTTP/2 response trailers are supported.</span></span>

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

<span data-ttu-id="6d5db-103">Im vorherigen Beispielcode:</span><span class="sxs-lookup"><span data-stu-id="6d5db-103">In the preceding example code:</span></span>

* <span data-ttu-id="6d5db-104">stellt `SupportsTrailers` sicher, dass Nachspanne für die Antwort unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="6d5db-104">`SupportsTrailers` ensures that trailers are supported for the response.</span></span>
* <span data-ttu-id="6d5db-105">fügt `DeclareTrailer` dem Antwortheader `Trailer` den angegebenen Namen für den Nachspann hinzu.</span><span class="sxs-lookup"><span data-stu-id="6d5db-105">`DeclareTrailer` adds the given trailer name to the `Trailer` response header.</span></span> <span data-ttu-id="6d5db-106">Das Deklarieren des Nachspanns einer Antwort ist optional, wird jedoch empfohlen.</span><span class="sxs-lookup"><span data-stu-id="6d5db-106">Declaring a response's trailers is optional, but recommended.</span></span> <span data-ttu-id="6d5db-107">Wenn `DeclareTrailer` aufgerufen wird, muss der Nachspann deklariert werden, bevor Antwortheader gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="6d5db-107">If `DeclareTrailer` is called, it must be before the response headers are sent.</span></span>
* <span data-ttu-id="6d5db-108">fügt `AppendTrailer` den Nachspann an.</span><span class="sxs-lookup"><span data-stu-id="6d5db-108">`AppendTrailer` appends the trailer.</span></span>
