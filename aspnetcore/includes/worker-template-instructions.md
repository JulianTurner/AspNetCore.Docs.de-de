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
# <a name="visual-studio"></a>[<span data-ttu-id="37663-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37663-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="37663-102">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="37663-102">Create a new project.</span></span>
1. <span data-ttu-id="37663-103">Wählen Sie **Workerdienst** aus.</span><span class="sxs-lookup"><span data-stu-id="37663-103">Select **Worker Service**.</span></span> <span data-ttu-id="37663-104">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="37663-104">Select **Next**.</span></span>
1. <span data-ttu-id="37663-105">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="37663-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="37663-106">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="37663-106">Select **Create**.</span></span>
1. <span data-ttu-id="37663-107">Wählen Sie im Dialogfeld **Neuen Workerdienst erstellen\*\*\*\*Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="37663-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="37663-108">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="37663-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="37663-109">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="37663-109">Create a new project.</span></span>
1. <span data-ttu-id="37663-110">Wählen Sie in der Randleiste unter **.NET Core** den Eintrag **App** aus.</span><span class="sxs-lookup"><span data-stu-id="37663-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="37663-111">Wählen Sie unter **ASP.NET Core** den Eintrag **Worker** aus.</span><span class="sxs-lookup"><span data-stu-id="37663-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="37663-112">Wählen Sie **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="37663-112">Select **Next**.</span></span>
1. <span data-ttu-id="37663-113">Wählen Sie **.NET Core 3.0** oder höher als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="37663-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="37663-114">Wählen Sie **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="37663-114">Select **Next**.</span></span>
1. <span data-ttu-id="37663-115">Geben Sie im Feld **Projektname** einen Namen an.</span><span class="sxs-lookup"><span data-stu-id="37663-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="37663-116">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="37663-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="37663-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="37663-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="37663-118">Rufen Sie die Vorlage „Workerdienst“ (`worker`) über eine Befehlsshell mit dem Befehl [dotnet new](/dotnet/core/tools/dotnet-new) auf.</span><span class="sxs-lookup"><span data-stu-id="37663-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="37663-119">Im folgenden Beispiel wird eine Workerdienstanwendung namens `ContosoWorker` erstellt.</span><span class="sxs-lookup"><span data-stu-id="37663-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="37663-120">Der Ordner für die App `ContosoWorker` wird automatisch erstellt, wenn der Befehl ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="37663-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
