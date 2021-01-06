# <a name="visual-studio"></a>[<span data-ttu-id="ce4fb-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ce4fb-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ce4fb-102">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-102">Create a new project.</span></span>
1. <span data-ttu-id="ce4fb-103">Wählen Sie **Workerdienst** aus.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-103">Select **Worker Service**.</span></span> <span data-ttu-id="ce4fb-104">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-104">Select **Next**.</span></span>
1. <span data-ttu-id="ce4fb-105">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ce4fb-106">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-106">Select **Create**.</span></span>
1. <span data-ttu-id="ce4fb-107">Wählen Sie im Dialogfeld **Neuen Workerdienst erstellen** **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ce4fb-108">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ce4fb-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="ce4fb-109">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-109">Create a new project.</span></span>
1. <span data-ttu-id="ce4fb-110">Wählen Sie in der Randleiste unter **.NET Core** den Eintrag **App** aus.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="ce4fb-111">Wählen Sie unter **ASP.NET Core** den Eintrag **Worker** aus.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="ce4fb-112">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-112">Select **Next**.</span></span>
1. <span data-ttu-id="ce4fb-113">Wählen Sie **.NET Core 3.0** oder höher als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="ce4fb-114">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-114">Select **Next**.</span></span>
1. <span data-ttu-id="ce4fb-115">Geben Sie im Feld **Projektname** einen Namen an.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="ce4fb-116">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="ce4fb-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ce4fb-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ce4fb-118">Rufen Sie die Vorlage „Workerdienst“ (`worker`) über eine Befehlsshell mit dem Befehl [dotnet new](/dotnet/core/tools/dotnet-new) auf.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="ce4fb-119">Im folgenden Beispiel wird eine Workerdienstanwendung namens `ContosoWorker` erstellt.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="ce4fb-120">Der Ordner für die App `ContosoWorker` wird automatisch erstellt, wenn der Befehl ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ce4fb-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
