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
ms.openlocfilehash: 7caea4089d3624d4c02db4b8adbe9edb73f3d31a
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552641"
---
> [!NOTE]
> Web-Apps vor ASP.NET Core 3.0 schreiben ein Protokoll vom Typ `LogLevel.Warning` pro Anforderung, wenn die angeforderte Kultur nicht unterstützt wird. Durch die Protokollierung einer `LogLevel.Warning` pro Anforderung können große Protokolldateien mit redundanten Informationen entstehen. Dieses Verhalten wurde in ASP.NET 3.0 geändert. Die `RequestLocalizationMiddleware` schreibt ein Protokoll vom Typ `LogLevel.Debug`, wodurch die Größe der Produktionsprotokolle reduziert wird.
