# <a name="visual-studio"></a>[<span data-ttu-id="1f30f-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f30f-101">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f30f-102">Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="1f30f-102">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="1f30f-103">Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus.</span><span class="sxs-lookup"><span data-stu-id="1f30f-103">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1f30f-104">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="1f30f-104">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1f30f-105">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="1f30f-105">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="1f30f-106">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="1f30f-106">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="1f30f-107">Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.</span><span class="sxs-lookup"><span data-stu-id="1f30f-107">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-code"></a>[<span data-ttu-id="1f30f-108">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f30f-108">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="1f30f-109">Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="1f30f-109">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1f30f-110">Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1f30f-110">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="1f30f-111">Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`.</span><span class="sxs-lookup"><span data-stu-id="1f30f-111">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1f30f-112">Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt.</span><span class="sxs-lookup"><span data-stu-id="1f30f-112">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1f30f-113">„Localhost“ dient nur Webanforderungen vom lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="1f30f-113">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="1f30f-114">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1f30f-114">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1f30f-115">Drücken Sie in Visual Studio **WAHL+CMD+EINGABETASTE**, um die Ausführung zu starten, ohne dabei den Debugger zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1f30f-115">From Visual Studio, press **Opt-Cmd-Return** to run without the debugger.</span></span> <span data-ttu-id="1f30f-116">Alternativ können Sie auch in der Menüleiste auf **Ausführen > Ohne Debugger starten** klicken.</span><span class="sxs-lookup"><span data-stu-id="1f30f-116">Alternatively, navigate to the menu bar and go to **Run>Start Without Debugging**.</span></span>

  <span data-ttu-id="1f30f-117">Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1f30f-117">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---
