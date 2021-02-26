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
HTTP-Nachspanne ähneln den HTTP-Headers, jedoch werden sie erst gesendet, nachdem der Antworttext gesendet wurde. Für IIS und HTTP.sys werden nur HTTP/2-Antwortnachspanne unterstützt.

```csharp
if (httpContext.Response.SupportsTrailers())
{
    httpContext.Response.DeclareTrailer("trailername"); 

    // Write body
    httpContext.Response.WriteAsync("Hello world");

    httpContext.Response.AppendTrailer("trailername", "TrailerValue");
}
```

Im vorherigen Beispielcode:

* stellt `SupportsTrailers` sicher, dass Nachspanne für die Antwort unterstützt werden.
* fügt `DeclareTrailer` dem Antwortheader `Trailer` den angegebenen Namen für den Nachspann hinzu. Das Deklarieren des Nachspanns einer Antwort ist optional, wird jedoch empfohlen. Wenn `DeclareTrailer` aufgerufen wird, muss der Nachspann deklariert werden, bevor Antwortheader gesendet werden.
* fügt `AppendTrailer` den Nachspann an.
