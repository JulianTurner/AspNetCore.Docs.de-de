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
ms.openlocfilehash: 164c9e8f73502fc627c4514bd683dc183d318b1d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552299"
---
Durch die Option „Zurücksetzen“ kann der Server eine HTTP/2-Anforderung mit einem angegebenen Fehlercode zurücksetzen. Eine Anforderung zum Zurücksetzen wird als abgebrochen betrachtet.

```csharp
var resetFeature = httpContext.Features.Get<IHttpResetFeature>();
resetFeature.Reset(errorCode: 2);
```

`Reset` gibt im vorangehenden Codebeispiel den Fehlercode `INTERNAL_ERROR` an. Weitere Informationen zu HTTP/2-Fehlercodes finden Sie im Abschnitt [HTTP/2-Spezifikationsfehlercode](https://tools.ietf.org/html/rfc7540#page-50).