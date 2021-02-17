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

  Ein Flag, das die Unterdrückung der HTTP-Antwortformatierung bewirkt.

* `-h|--header`

  Legt einen HTTP-Anforderungsheader fest. Die folgenden zwei Werteformate werden unterstützt:

  * `{header}={value}`
  * `{header}:{value}`

* `--response:body`

  Gibt eine Datei an, in die der HTTP-Antworttext geschrieben werden soll. Beispiel: `--response:body "C:\response.json"`. Wenn die Datei nicht vorhanden ist, wird sie erstellt.

* `--response:headers`

  Gibt eine Datei an, in die die HTTP-Antwortheader geschrieben werden sollen. Beispiel: `--response:headers "C:\response.txt"`. Wenn die Datei nicht vorhanden ist, wird sie erstellt.

* `-s|--streaming`

  Ein Flag, das das Streaming der HTTP-Antwort aktiviert.
