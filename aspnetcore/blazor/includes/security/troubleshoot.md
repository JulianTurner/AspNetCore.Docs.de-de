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
ms.openlocfilehash: 4e7c0e9b0a164e0181af5d6baaedf0669c1c06aa
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552938"
---
## <a name="troubleshoot"></a><span data-ttu-id="bb821-101">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="bb821-101">Troubleshoot</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="common-errors"></a><span data-ttu-id="bb821-102">Häufige Fehler</span><span class="sxs-lookup"><span data-stu-id="bb821-102">Common errors</span></span>

* <span data-ttu-id="bb821-103">Nicht autorisierter Client für AAD</span><span class="sxs-lookup"><span data-stu-id="bb821-103">Unauthorized client for AAD</span></span>

  > <span data-ttu-id="bb821-104">Info: Die Autorisierung von Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="bb821-104">info: Microsoft.AspNetCore.Authorization.DefaultAuthorizationService[2] Authorization failed.</span></span> <span data-ttu-id="bb821-105">Diese Anforderungen wurden nicht erfüllt: DenyAnonymousAuthorizationRequirement: Erfordert einen authentifizierten Benutzer.</span><span class="sxs-lookup"><span data-stu-id="bb821-105">These requirements were not met: DenyAnonymousAuthorizationRequirement: Requires an authenticated user.</span></span>

  <span data-ttu-id="bb821-106">Anmelderückruffehler von AAD:</span><span class="sxs-lookup"><span data-stu-id="bb821-106">Login callback error from AAD:</span></span>

  * <span data-ttu-id="bb821-107">Fehler: `unauthorized_client`</span><span class="sxs-lookup"><span data-stu-id="bb821-107">Error: `unauthorized_client`</span></span>
  * <span data-ttu-id="bb821-108">Description (Beschreibung): `AADB2C90058: The provided application is not configured to allow public clients.`</span><span class="sxs-lookup"><span data-stu-id="bb821-108">Description: `AADB2C90058: The provided application is not configured to allow public clients.`</span></span>

  <span data-ttu-id="bb821-109">So beheben Sie den Fehler</span><span class="sxs-lookup"><span data-stu-id="bb821-109">To resolve the error:</span></span>

  1. <span data-ttu-id="bb821-110">Greifen Sie im Azure-Portal auf das [Manifest der App](/azure/active-directory/develop/reference-app-manifest) zu.</span><span class="sxs-lookup"><span data-stu-id="bb821-110">In the Azure portal, access the [app's manifest](/azure/active-directory/develop/reference-app-manifest).</span></span>
  1. <span data-ttu-id="bb821-111">Legen Sie das [`allowPublicClient`-Attribut](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) auf `null` oder `true` fest.</span><span class="sxs-lookup"><span data-stu-id="bb821-111">Set the [`allowPublicClient` attribute](/azure/active-directory/develop/reference-app-manifest#allowpublicclient-attribute) to `null` or `true`.</span></span>

::: moniker-end

### <a name="cookies-and-site-data"></a><span data-ttu-id="bb821-112">Cookies und Standortdaten</span><span class="sxs-lookup"><span data-stu-id="bb821-112">Cookies and site data</span></span>

<span data-ttu-id="bb821-113">Cookies und Standortdaten können über App-Updates hinweg beibehalten werden und das Testen und die Problembehandlung beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="bb821-113">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="bb821-114">Entfernen Sie Folgendes, wenn Sie Änderungen am App-Code, Änderungen an den Benutzerkonten beim Anbieter oder Konfigurationsänderungen an Anbieter-Apps vornehmen:</span><span class="sxs-lookup"><span data-stu-id="bb821-114">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="bb821-115">cookies für Benutzeranmeldung</span><span class="sxs-lookup"><span data-stu-id="bb821-115">User sign-in cookies</span></span>
* <span data-ttu-id="bb821-116">cookies für App</span><span class="sxs-lookup"><span data-stu-id="bb821-116">App cookies</span></span>
* <span data-ttu-id="bb821-117">Zwischengespeicherte und gespeicherte Standortdaten</span><span class="sxs-lookup"><span data-stu-id="bb821-117">Cached and stored site data</span></span>

<span data-ttu-id="bb821-118">Ein Ansatz, um zu verhindern, dass veraltete cookies und Standortdaten das Testen und die Problembehandlung beeinträchtigen, ist folgender:</span><span class="sxs-lookup"><span data-stu-id="bb821-118">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="bb821-119">Browser konfigurieren</span><span class="sxs-lookup"><span data-stu-id="bb821-119">Configure a browser</span></span>
  * <span data-ttu-id="bb821-120">Verwenden Sie zum Testen einen Browser, den Sie so konfigurieren können, dass alle cookies und Standortdaten jedes Mal gelöscht werden, wenn der Browser geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="bb821-120">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
  * <span data-ttu-id="bb821-121">Stellen Sie sicher, dass der Browser manuell oder durch die IDE für alle Änderungen an der App, dem Testbenutzer oder der Anbieterkonfiguration geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="bb821-121">Make sure that the browser is closed manually or by the IDE for any change to the app, test user, or provider configuration.</span></span>
* <span data-ttu-id="bb821-122">Verwenden Sie einen benutzerdefinierten Befehl, um in Visual Studio einen Browser im Inkognito- oder im privaten Modus zu öffnen:</span><span class="sxs-lookup"><span data-stu-id="bb821-122">Use a custom command to open a browser in incognito or private mode in Visual Studio:</span></span>
  * <span data-ttu-id="bb821-123">Öffnen Sie mithilfe der Schaltfläche **Ausführen** von Visual Studio das Dialogfeld **Browserauswahl**.</span><span class="sxs-lookup"><span data-stu-id="bb821-123">Open **Browse With** dialog box from Visual Studio's **Run** button.</span></span>
  * <span data-ttu-id="bb821-124">Wählen Sie die Schaltfläche **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="bb821-124">Select the **Add** button.</span></span>
  * <span data-ttu-id="bb821-125">Geben Sie im Feld **Programm** den Pfad zu Ihrem Browser an.</span><span class="sxs-lookup"><span data-stu-id="bb821-125">Provide the path to your browser in the **Program** field.</span></span> <span data-ttu-id="bb821-126">Die folgenden Pfade für ausführbare Dateien sind typische Installationspfade für Windows 10.</span><span class="sxs-lookup"><span data-stu-id="bb821-126">The following executable paths are typical installation locations for Windows 10.</span></span> <span data-ttu-id="bb821-127">Wenn Ihr Browser an einem anderen Speicherort installiert ist oder Sie nicht Windows 10 verwenden, geben Sie den Pfad zur ausführbaren Datei des Browsers an.</span><span class="sxs-lookup"><span data-stu-id="bb821-127">If your browser is installed in a different location or you aren't using Windows 10, provide the path to the browser's executable.</span></span>
    * <span data-ttu-id="bb821-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span><span class="sxs-lookup"><span data-stu-id="bb821-128">Microsoft Edge: `C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe`</span></span>
    * <span data-ttu-id="bb821-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span><span class="sxs-lookup"><span data-stu-id="bb821-129">Google Chrome: `C:\Program Files (x86)\Google\Chrome\Application\chrome.exe`</span></span>
    * <span data-ttu-id="bb821-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span><span class="sxs-lookup"><span data-stu-id="bb821-130">Mozilla Firefox: `C:\Program Files\Mozilla Firefox\firefox.exe`</span></span>
  * <span data-ttu-id="bb821-131">Geben Sie im Feld **Argumente** die Befehlszeilenoption an, die der Browser verwendet, um im Inkognito- oder im privaten Modus geöffnet zu werden.</span><span class="sxs-lookup"><span data-stu-id="bb821-131">In the **Arguments** field, provide the command-line option that the browser uses to open in incognito or private mode.</span></span> <span data-ttu-id="bb821-132">Für einige Browser ist die URL der App erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bb821-132">Some browsers require the URL of the app.</span></span>
    * <span data-ttu-id="bb821-133">Microsoft Edge: Verwenden Sie `-inprivate`.</span><span class="sxs-lookup"><span data-stu-id="bb821-133">Microsoft Edge: Use `-inprivate`.</span></span>
    * <span data-ttu-id="bb821-134">Google Chrome: Verwenden Sie `--incognito --new-window {URL}`, wobei der Platzhalter `{URL}` die zu öffnende URL ist (z. B. `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="bb821-134">Google Chrome: Use `--incognito --new-window {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
    * <span data-ttu-id="bb821-135">Mozilla Firefox: Verwenden Sie `-private -url {URL}`, wobei der Platzhalter `{URL}` die zu öffnende URL ist (z. B. `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="bb821-135">Mozilla Firefox: Use `-private -url {URL}`, where the placeholder `{URL}` is the URL to open (for example, `https://localhost:5001`).</span></span>
  * <span data-ttu-id="bb821-136">Geben Sie im Feld **Anzeigename** einen Namen ein.</span><span class="sxs-lookup"><span data-stu-id="bb821-136">Provide a name in the **Friendly name** field.</span></span> <span data-ttu-id="bb821-137">Beispielsweise `Firefox Auth Testing`.</span><span class="sxs-lookup"><span data-stu-id="bb821-137">For example, `Firefox Auth Testing`.</span></span>
  * <span data-ttu-id="bb821-138">Klicken Sie auf die Schaltfläche **OK**.</span><span class="sxs-lookup"><span data-stu-id="bb821-138">Select the **OK** button.</span></span>
  * <span data-ttu-id="bb821-139">Um zu vermeiden, dass das Browserprofil für jede einzelne Testiteration einer App ausgewählt werden muss, legen Sie das Profil mithilfe der Schaltfläche **Als Standard festlegen** als Standard fest.</span><span class="sxs-lookup"><span data-stu-id="bb821-139">To avoid having to select the browser profile for each iteration of testing with an app, set the profile as the default with the **Set as Default** button.</span></span>
  * <span data-ttu-id="bb821-140">Stellen Sie sicher, dass der Browser von der IDE für alle Änderungen an der App, dem Testbenutzer oder der Anbieterkonfiguration geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="bb821-140">Make sure that the browser is closed by the IDE for any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="bb821-141">Ausführen der Server-App</span><span class="sxs-lookup"><span data-stu-id="bb821-141">Run the Server app</span></span>

<span data-ttu-id="bb821-142">Stellen Sie beim Testen und Beheben von Problemen bei einer gehosteten Blazor-App sicher, dass Sie die Blazor aus dem **`Server`** -Projekt ausführen.</span><span class="sxs-lookup"><span data-stu-id="bb821-142">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **`Server`** project.</span></span> <span data-ttu-id="bb821-143">Vergewissern Sie sich z. B. in Visual Studio, dass das Serverprojekt im **Projektmappen-Explorer** markiert ist, bevor Sie die App mit einem der folgenden Ansätze starten:</span><span class="sxs-lookup"><span data-stu-id="bb821-143">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="bb821-144">Wählen Sie die Schaltfläche **Ausführen**.</span><span class="sxs-lookup"><span data-stu-id="bb821-144">Select the **Run** button.</span></span>
* <span data-ttu-id="bb821-145">Verwenden Sie im Menü **Debuggen** > **Debuggen starten**.</span><span class="sxs-lookup"><span data-stu-id="bb821-145">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="bb821-146">Drücken Sie <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="bb821-146">Press <kbd>F5</kbd>.</span></span>

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a><span data-ttu-id="bb821-147">Überprüfen des Inhalts eines JSON Web Tokens (JWT)</span><span class="sxs-lookup"><span data-stu-id="bb821-147">Inspect the content of a JSON Web Token (JWT)</span></span>

<span data-ttu-id="bb821-148">Verwenden Sie zum Decodieren eines JSON Web Tokens (JWT) das Tool [jwt.ms](https://jwt.ms/) von Microsoft.</span><span class="sxs-lookup"><span data-stu-id="bb821-148">To decode a JSON Web Token (JWT), use Microsoft's [jwt.ms](https://jwt.ms/) tool.</span></span> <span data-ttu-id="bb821-149">Werte in der Benutzeroberfläche verlassen nie Ihren Browser.</span><span class="sxs-lookup"><span data-stu-id="bb821-149">Values in the UI never leave your browser.</span></span>
