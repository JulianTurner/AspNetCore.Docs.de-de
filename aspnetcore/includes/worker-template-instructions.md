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
ms.openlocfilehash: 34e12afd6bc7015e4609fbdf773259ed5545809b
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552100"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Erstellen Sie ein neues Projekt.
1. Wählen Sie **Workerdienst** aus. Klicken Sie auf **Weiter**.
1. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Klicken Sie auf **Erstellen**.
1. Wählen Sie im Dialogfeld **Neuen Workerdienst erstellen****Erstellen** aus.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Erstellen Sie ein neues Projekt.
1. Wählen Sie in der Randleiste unter **.NET Core** den Eintrag **App** aus.
1. Wählen Sie unter **ASP.NET Core** den Eintrag **Worker** aus. Wählen Sie **Weiter** aus.
1. Wählen Sie **.NET Core 3.0** oder höher als **Zielframework** aus. Wählen Sie **Weiter** aus.
1. Geben Sie im Feld **Projektname** einen Namen an. Klicken Sie auf **Erstellen**.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Rufen Sie die Vorlage „Workerdienst“ (`worker`) über eine Befehlsshell mit dem Befehl [dotnet new](/dotnet/core/tools/dotnet-new) auf. Im folgenden Beispiel wird eine Workerdienstanwendung namens `ContosoWorker` erstellt. Der Ordner für die App `ContosoWorker` wird automatisch erstellt, wenn der Befehl ausgeführt wird.

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
