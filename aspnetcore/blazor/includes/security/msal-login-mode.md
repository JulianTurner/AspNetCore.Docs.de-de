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
ms.openlocfilehash: 1b045d437d1a16eabc0ab41573c8b66d9c4bb77e
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551988"
---
Das Framework ist standardmäßig auf den Popup-Anmeldemodus festgelegt und greift auf den Umleitungsanmeldemodus zurück, wenn kein Popup geöffnet werden kann. Konfigurieren Sie MSAL, um den Umleitungsanmeldemodus zu verwenden, indem Sie die `LoginMode`-Eigenschaft von <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> auf `redirect` festlegen:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.LoginMode = "redirect";
});
```

Die Standardeinstellung ist `popup`, und der Zeichenfolgenwert berücksichtigt keine Groß-/Kleinschreibung.
