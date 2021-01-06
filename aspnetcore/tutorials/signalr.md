---
title: Erste Schritte mit ASP.NET Core SignalR
author: bradygaster
description: In diesem Tutorial erstellen Sie eine Chat-App, die ASP.NET Core SignalR verwendet.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
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
uid: tutorials/signalr
ms.openlocfilehash: 1dad2264250bf43ec6c1df679b8754a82a5addfd
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "95417668"
---
# <a name="tutorial-get-started-with-aspnet-core-no-locsignalr"></a><span data-ttu-id="61c71-103">Tutorial: Erste Schritte mit ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="61c71-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="61c71-104">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR beschrieben.</span><span class="sxs-lookup"><span data-stu-id="61c71-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="61c71-105">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="61c71-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="61c71-106">Erstellen Sie ein Webprojekt.</span><span class="sxs-lookup"><span data-stu-id="61c71-106">Create a web project.</span></span>
> * <span data-ttu-id="61c71-107">Fügen Sie die SignalR-Clientbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="61c71-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="61c71-108">Erstellen Sie einen SignalR-Hub.</span><span class="sxs-lookup"><span data-stu-id="61c71-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="61c71-109">Konfigurieren Sie das Projekt für die Verwendung von SignalR.</span><span class="sxs-lookup"><span data-stu-id="61c71-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="61c71-110">Hinzufügen von Code, mit dem Nachrichten von jedem Client an alle verbundene Clients gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="61c71-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="61c71-111">Am Ende verfügen Sie über eine funktionierende Chat-App:</span><span class="sxs-lookup"><span data-stu-id="61c71-111">At the end, you'll have a working chat app:</span></span>

![SignalR (Beispiel-App)](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="61c71-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="61c71-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="61c71-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c71-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="61c71-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c71-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="61c71-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="61c71-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="61c71-117">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="61c71-117">Create a web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="61c71-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c71-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="61c71-119">Klicken Sie im Menü auf **Datei > Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="61c71-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="61c71-120">Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **ASP.NET Core-Webanwendung** aus, und klicken Sie dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="61c71-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="61c71-121">Geben Sie im Dialogfeld **Neues Projekt konfigurieren** dem Projekt den Namen *SignalRChat*, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="61c71-122">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.1** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-122">In the **Create a new ASP.NET Core web Application** dialog, select **.NET Core** and **ASP.NET Core 3.1**.</span></span> 

* <span data-ttu-id="61c71-123">Klicken Sie auf **Webanwendung**, um ein Projekt zu erstellen, das Razor Pages verwendet, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="61c71-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c71-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="61c71-126">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) in dem Ordner, in dem der neue Projektordner erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="61c71-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="61c71-127">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="61c71-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   cd SignalRChat
   code -r .
   ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="61c71-128">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="61c71-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="61c71-129">Klicken Sie im Menü auf **Datei > Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="61c71-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="61c71-130">Wählen Sie **.NET Core > App > Webanwendung** aus (nicht **Webanwendung (Model View Controller)** auswählen), und klicken Sie dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="61c71-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="61c71-131">Stellen Sie sicher, dass das **Zielframework** auf **.NET Core 3.1** festgelegt ist, und wählen Sie dann **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-131">Make sure the **Target Framework** is set to **.NET Core 3.1**, and then select **Next**.</span></span>

* <span data-ttu-id="61c71-132">Nennen Sie das Projekt *SignalRChat*, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="61c71-133">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="61c71-133">Add the SignalR client library</span></span>

<span data-ttu-id="61c71-134">Die SignalR-Serverbibliothek ist im freigegebenen ASP.NET Core 3.1-Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="61c71-134">The SignalR server library is included in the ASP.NET Core 3.1 shared framework.</span></span> <span data-ttu-id="61c71-135">Die JavaScript-Clientbibliothek ist nicht automatisch im Projekt enthalten.</span><span class="sxs-lookup"><span data-stu-id="61c71-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="61c71-136">In diesem Tutorial verwenden Sie den Bibliotheks-Manager (LibMan), um die Clientbibliothek von *unpkg* abzurufen.</span><span class="sxs-lookup"><span data-stu-id="61c71-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="61c71-137">unpkg ist ein CDN (Content Delivery Network), mit dem Sie alles bereitstellen können, was im npm (Node.js-Paket-Manager) zu finden ist.</span><span class="sxs-lookup"><span data-stu-id="61c71-137">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="61c71-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c71-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="61c71-139">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Hinzufügen** > **Clientseitige Bibliothek**.</span><span class="sxs-lookup"><span data-stu-id="61c71-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="61c71-140">Wählen Sie **unpkg** im Dialogfeld **Add Client-Side Library** (Clientseitige Bibliothek hinzufügen) als **Anbieter** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="61c71-141">Geben Sie für **Bibliothek**`@microsoft/signalr@latest` ein.</span><span class="sxs-lookup"><span data-stu-id="61c71-141">For **Library**, enter `@microsoft/signalr@latest`.</span></span>

* <span data-ttu-id="61c71-142">Klicken Sie auf **Choose specific files** (Spezifische Dateien auswählen), erweitern Sie den Ordner *dist/browser*, und wählen Sie *signalr.js* und *signalr.min.js* aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="61c71-143">Legen Sie *wwwroot/js/signalr/* als **Zielspeicherort** fest, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="61c71-143">Set **Target Location** to *wwwroot/js/signalr/*, and select **Install**.</span></span>

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Bibliothek](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  <span data-ttu-id="61c71-145">Der Ordner *wwwroot/js/signalr* wird von LibMan erstellt, und die ausgewählten Dateien werden in ihn hineinkopiert.</span><span class="sxs-lookup"><span data-stu-id="61c71-145">LibMan creates a *wwwroot/js/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="61c71-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c71-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="61c71-147">Führen Sie den folgenden Befehl über das integrierte Terminal aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="61c71-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="61c71-148">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="61c71-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="61c71-149">Es kann einige Sekunden dauern, bis die Ausgabe angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="61c71-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="61c71-150">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="61c71-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="61c71-151">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="61c71-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="61c71-152">Das Kopieren der Dateien in den Zielordner *wwwroot/js/signalr*.</span><span class="sxs-lookup"><span data-stu-id="61c71-152">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="61c71-153">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="61c71-153">Copy only the specified files.</span></span>

  <span data-ttu-id="61c71-154">Die Ausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="61c71-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="61c71-155">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="61c71-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="61c71-156">Führen Sie den folgenden Befehl über das **Terminal** aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="61c71-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="61c71-157">Navigieren Sie zum Projektordner (der die Datei *SignalRChat.csproj* enthält).</span><span class="sxs-lookup"><span data-stu-id="61c71-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="61c71-158">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="61c71-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="61c71-159">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="61c71-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="61c71-160">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="61c71-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="61c71-161">Das Kopieren der Dateien in den Zielordner *wwwroot/js/signalr*.</span><span class="sxs-lookup"><span data-stu-id="61c71-161">Copy files to the *wwwroot/js/signalr* destination.</span></span>
  * <span data-ttu-id="61c71-162">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="61c71-162">Copy only the specified files.</span></span>

  <span data-ttu-id="61c71-163">Die Ausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="61c71-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="61c71-164">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="61c71-164">Create a SignalR hub</span></span>

<span data-ttu-id="61c71-165">Ein *Hub* ist eine Klasse, die als grundlegende Pipeline verwendet wird, mit der Client/Server-Kommunikation verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="61c71-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="61c71-166">Erstellen Sie im SignalRChat-Projektordner einen *Hubs*-Ordner.</span><span class="sxs-lookup"><span data-stu-id="61c71-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="61c71-167">Erstellen Sie im *Hubs*-Ordner eine *ChatHub.cs*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="61c71-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="61c71-168">Die `ChatHub`-Klasse erbt von der `Hub`-Klasse von SignalR.</span><span class="sxs-lookup"><span data-stu-id="61c71-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="61c71-169">Die `Hub`-Klasse verwaltet Verbindungen, Gruppen und Messaging.</span><span class="sxs-lookup"><span data-stu-id="61c71-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="61c71-170">Die `SendMessage`-Methode kann von einem verbundenen Client aufgerufen werden, um eine Nachricht an alle Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="61c71-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="61c71-171">JavaScript-Clientcode, in dem die Methode aufgerufen wird, wird später in diesem Tutorial vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="61c71-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="61c71-172">SignalR-Code ist asynchron, damit die maximale Skalierbarkeit gewährleistet werden kann.</span><span class="sxs-lookup"><span data-stu-id="61c71-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-no-locsignalr"></a><span data-ttu-id="61c71-173">Konfigurieren von SignalR</span><span class="sxs-lookup"><span data-stu-id="61c71-173">Configure SignalR</span></span>

<span data-ttu-id="61c71-174">Der SignalR-Server muss so konfiguriert werden, dass er SignalR-Anforderungen an SignalR weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="61c71-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="61c71-175">Fügen Sie der *Startup.cs*-Datei den folgenden hervorgehobenen Code zu.</span><span class="sxs-lookup"><span data-stu-id="61c71-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  <span data-ttu-id="61c71-176">Durch diese Änderungen wird der ASP.NET Core-Abhängigkeitsinjektion und den Routingsystemen SignalR hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="61c71-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="61c71-177">Hinzufügen von SignalR-Clientcode</span><span class="sxs-lookup"><span data-stu-id="61c71-177">Add SignalR client code</span></span>

* <span data-ttu-id="61c71-178">Ersetzen Sie den Inhalt in *Pages\Index.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="61c71-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="61c71-179">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="61c71-179">The preceding code:</span></span>

  * <span data-ttu-id="61c71-180">erstellt Textfelder für den Namen und den Nachrichtentext sowie eine Senden-Schaltfläche</span><span class="sxs-lookup"><span data-stu-id="61c71-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="61c71-181">erstellt eine Liste mit `id="messagesList"` zum Anzeigen von Nachrichten, die vom SignalR-Hub empfangen werden</span><span class="sxs-lookup"><span data-stu-id="61c71-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="61c71-182">schließt Skriptverweise auf SignalR sowie den *chat.js*-Anwendungscode ein, den Sie im folgenden Schritt erstellen</span><span class="sxs-lookup"><span data-stu-id="61c71-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="61c71-183">Erstellen Sie im *wwwroot/js*-Ordner eine *chat.js*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="61c71-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="61c71-184">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="61c71-184">The preceding code:</span></span>

  * <span data-ttu-id="61c71-185">erstellt und startet eine Verbindung</span><span class="sxs-lookup"><span data-stu-id="61c71-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="61c71-186">fügt einen Handler zur Senden-Schaltfläche hinzu, der Nachrichten an den Hub sendet</span><span class="sxs-lookup"><span data-stu-id="61c71-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="61c71-187">fügt einen Handler zum Verbindungsobjekt hinzu, der Nachrichten vom Hub empfängt und diese der Liste hinzufügt</span><span class="sxs-lookup"><span data-stu-id="61c71-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="61c71-188">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="61c71-188">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="61c71-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c71-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="61c71-190">Drücken Sie **STRG+F5**, um die App ohne Debugging zu starten.</span><span class="sxs-lookup"><span data-stu-id="61c71-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="61c71-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c71-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="61c71-192">Führen Sie über das integrierte Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="61c71-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="61c71-193">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="61c71-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="61c71-194">Wählen Sie im Menü **Ausführen > Starten ohne Debuggen** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="61c71-195">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="61c71-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="61c71-196">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Nachricht senden**.</span><span class="sxs-lookup"><span data-stu-id="61c71-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="61c71-197">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="61c71-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR (Beispiel-App)](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="61c71-199">Wenn die App nicht funktioniert, öffnen Sie die Browser-Entwicklungstools (F12), und wechseln Sie zur Konsole.</span><span class="sxs-lookup"><span data-stu-id="61c71-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="61c71-200">Möglicherweise werden Fehler in Bezug auf Ihren HTML- und JavaScript-Code angezeigt.</span><span class="sxs-lookup"><span data-stu-id="61c71-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="61c71-201">Nehmen wir an, dass Sie z.B. *signalr.js* in einen anderen Ordner als vorgeschrieben platziert haben.</span><span class="sxs-lookup"><span data-stu-id="61c71-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="61c71-202">In diesem Fall funktioniert der Verweis auf diese Datei nicht, und Ihnen wird der Fehler 404 in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="61c71-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="61c71-203">![Fehler „signalr.js nicht gefunden“](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="61c71-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="61c71-204">Wenn Sie den Fehler ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome erhalten, führen Sie folgende Befehle aus, um das Entwicklungszertifikat zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="61c71-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="61c71-205">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR beschrieben.</span><span class="sxs-lookup"><span data-stu-id="61c71-205">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="61c71-206">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="61c71-206">You learn how to:</span></span> 

> [!div class="checklist"]  
> * <span data-ttu-id="61c71-207">Erstellen Sie ein Webprojekt.</span><span class="sxs-lookup"><span data-stu-id="61c71-207">Create a web project.</span></span>   
> * <span data-ttu-id="61c71-208">Fügen Sie die SignalR-Clientbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="61c71-208">Add the SignalR client library.</span></span>   
> * <span data-ttu-id="61c71-209">Erstellen Sie einen SignalR-Hub.</span><span class="sxs-lookup"><span data-stu-id="61c71-209">Create a SignalR hub.</span></span> 
> * <span data-ttu-id="61c71-210">Konfigurieren Sie das Projekt für die Verwendung von SignalR.</span><span class="sxs-lookup"><span data-stu-id="61c71-210">Configure the project to use SignalR.</span></span> 
> * <span data-ttu-id="61c71-211">Hinzufügen von Code, mit dem Nachrichten von jedem Client an alle verbundene Clients gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="61c71-211">Add code that sends messages from any client to all connected clients.</span></span>  
<span data-ttu-id="61c71-212">Am Ende verfügen Sie über eine funktionierende Chat-App: ![SignalR-Beispiel-App](signalr/_static/2.x/signalr-get-started-finished.png).</span><span class="sxs-lookup"><span data-stu-id="61c71-212">At the end, you'll have a working chat app: ![SignalR sample app](signalr/_static/2.x/signalr-get-started-finished.png)</span></span>   

## <a name="prerequisites"></a><span data-ttu-id="61c71-213">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="61c71-213">Prerequisites</span></span>    

# <a name="visual-studio"></a>[<span data-ttu-id="61c71-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c71-214">Visual Studio</span></span>](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-code"></a>[<span data-ttu-id="61c71-215">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c71-215">Visual Studio Code</span></span>](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="61c71-216">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="61c71-216">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a><span data-ttu-id="61c71-217">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="61c71-217">Create a web project</span></span> 

# <a name="visual-studio"></a>[<span data-ttu-id="61c71-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c71-218">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="61c71-219">Klicken Sie im Menü auf **Datei > Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="61c71-219">From the menu, select **File > New Project**.</span></span> 

* <span data-ttu-id="61c71-220">Wählen Sie im Dialogfeld **Neues Projekt** **Installiert > Visual C# > Web > ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-220">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="61c71-221">Benennen Sie das Projekt mit *SignalRChat*.</span><span class="sxs-lookup"><span data-stu-id="61c71-221">Name the project *SignalRChat*.</span></span>   

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* <span data-ttu-id="61c71-223">Klicken Sie auf **Webanwendung**, um ein Projekt zu erstellen, das Razor Pages verwendet.</span><span class="sxs-lookup"><span data-stu-id="61c71-223">Select **Web Application** to create a project that uses Razor Pages.</span></span>   

* <span data-ttu-id="61c71-224">Wählen Sie **.NET Core** als Zielframework sowie **ASP.NET Core 2.2** aus, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="61c71-224">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>    

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-code"></a>[<span data-ttu-id="61c71-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c71-226">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="61c71-227">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) in dem Ordner, in dem der neue Projektordner erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="61c71-227">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>  

* <span data-ttu-id="61c71-228">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="61c71-228">Run the following commands:</span></span>   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat   
   code -r SignalRChat    
   ```  

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="61c71-229">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="61c71-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="61c71-230">Klicken Sie im Menü auf **Datei > Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="61c71-230">From the menu, select **File > New Solution**.</span></span>    

* <span data-ttu-id="61c71-231">Klicken Sie auf **.NET Core > App > ASP.NET Core-Web-App** (Wählen Sie nicht **ASP.NET Core-Web-App (MVC)** aus).</span><span class="sxs-lookup"><span data-stu-id="61c71-231">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>  

* <span data-ttu-id="61c71-232">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="61c71-232">Select **Next**.</span></span>  

* <span data-ttu-id="61c71-233">Nennen Sie das Projekt *SignalRChat*, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-233">Name the project *SignalRChat*, and then select **Create**.</span></span> 

--- 

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="61c71-234">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="61c71-234">Add the SignalR client library</span></span> 

<span data-ttu-id="61c71-235">Die SignalR-Serverbibliothek ist im Metapaket `Microsoft.AspNetCore.App` enthalten.</span><span class="sxs-lookup"><span data-stu-id="61c71-235">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="61c71-236">Die JavaScript-Clientbibliothek ist nicht automatisch im Projekt enthalten.</span><span class="sxs-lookup"><span data-stu-id="61c71-236">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="61c71-237">In diesem Tutorial verwenden Sie den Bibliotheks-Manager (LibMan), um die Clientbibliothek von *unpkg* abzurufen.</span><span class="sxs-lookup"><span data-stu-id="61c71-237">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="61c71-238">unpkg ist ein CDN (Content Delivery Network), mit dem Sie alles bereitstellen können, was im npm (Node.js-Paket-Manager) zu finden ist.</span><span class="sxs-lookup"><span data-stu-id="61c71-238">unpkg is a content delivery network (CDN) that can deliver anything found in npm, the Node.js package manager.</span></span>   

# <a name="visual-studio"></a>[<span data-ttu-id="61c71-239">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c71-239">Visual Studio</span></span>](#tab/visual-studio/)  

* <span data-ttu-id="61c71-240">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Hinzufügen** > **Clientseitige Bibliothek**.</span><span class="sxs-lookup"><span data-stu-id="61c71-240">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>  

* <span data-ttu-id="61c71-241">Wählen Sie **unpkg** im Dialogfeld **Add Client-Side Library** (Clientseitige Bibliothek hinzufügen) als **Anbieter** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-241">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span> 

* <span data-ttu-id="61c71-242">Geben Sie `@microsoft/signalr@3` für die **Bibliothek** ein, und wählen Sie die neueste Version aus, die keine Vorschauversion ist.</span><span class="sxs-lookup"><span data-stu-id="61c71-242">For **Library**, enter `@microsoft/signalr@3`, and select the latest version that isn't preview.</span></span>  

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Bibliothek](signalr/_static/2.x/libman1.png)   

* <span data-ttu-id="61c71-244">Klicken Sie auf **Choose specific files** (Spezifische Dateien auswählen), erweitern Sie den Ordner *dist/browser*, und wählen Sie *signalr.js* und *signalr.min.js* aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-244">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span> 

* <span data-ttu-id="61c71-245">Legen Sie *wwwroot/lib/signalr/* als **Zielspeicherort** fest, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="61c71-245">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>    

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Dateien und des Zielspeicherorts](signalr/_static/2.x/libman2.png) 

  <span data-ttu-id="61c71-247">Der Ordner *wwwroot/lib/signalr* wird von LibMan erstellt, und die ausgewählten Dateien werden in ihn hineinkopiert.</span><span class="sxs-lookup"><span data-stu-id="61c71-247">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>    

# <a name="visual-studio-code"></a>[<span data-ttu-id="61c71-248">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c71-248">Visual Studio Code</span></span>](#tab/visual-studio-code/)    

* <span data-ttu-id="61c71-249">Führen Sie den folgenden Befehl über das integrierte Terminal aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="61c71-249">In the integrated terminal, run the following command to install LibMan.</span></span>  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="61c71-250">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="61c71-250">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="61c71-251">Es kann einige Sekunden dauern, bis die Ausgabe angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="61c71-251">You might have to wait a few seconds before seeing output.</span></span> 

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="61c71-252">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="61c71-252">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="61c71-253">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="61c71-253">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="61c71-254">Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="61c71-254">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="61c71-255">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="61c71-255">Copy only the specified files.</span></span>  

  <span data-ttu-id="61c71-256">Die Ausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="61c71-256">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.0.1" to "wwwroot/lib/signalr" 
  ```   

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="61c71-257">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="61c71-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)   

* <span data-ttu-id="61c71-258">Führen Sie den folgenden Befehl über das **Terminal** aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="61c71-258">In the **Terminal**, run the following command to install LibMan.</span></span> 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* <span data-ttu-id="61c71-259">Navigieren Sie zum Projektordner (der die Datei *SignalRChat.csproj* enthält).</span><span class="sxs-lookup"><span data-stu-id="61c71-259">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>   

* <span data-ttu-id="61c71-260">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="61c71-260">Run the following command to get the SignalR client library by using LibMan.</span></span>    

  ```console    
  libman install @microsoft/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js 
  ```   

  <span data-ttu-id="61c71-261">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="61c71-261">The parameters specify the following options:</span></span> 
  * <span data-ttu-id="61c71-262">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="61c71-262">Use the unpkg provider.</span></span> 
  * <span data-ttu-id="61c71-263">Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="61c71-263">Copy files to the *wwwroot/lib/signalr* destination.</span></span>    
  * <span data-ttu-id="61c71-264">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="61c71-264">Copy only the specified files.</span></span>  

  <span data-ttu-id="61c71-265">Die Ausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="61c71-265">The output looks like the following example:</span></span>  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@microsoft/signalr@3.x.x" to "wwwroot/lib/signalr" 
  ```   

--- 

## <a name="create-a-no-locsignalr-hub"></a><span data-ttu-id="61c71-266">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="61c71-266">Create a SignalR hub</span></span>   

<span data-ttu-id="61c71-267">Ein *Hub* ist eine Klasse, die als grundlegende Pipeline verwendet wird, mit der Client/Server-Kommunikation verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="61c71-267">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>   

* <span data-ttu-id="61c71-268">Erstellen Sie im SignalRChat-Projektordner einen *Hubs*-Ordner.</span><span class="sxs-lookup"><span data-stu-id="61c71-268">In the SignalRChat project folder, create a *Hubs* folder.</span></span>  

* <span data-ttu-id="61c71-269">Erstellen Sie im *Hubs*-Ordner eine *ChatHub.cs*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="61c71-269">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span> 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  <span data-ttu-id="61c71-270">Die `ChatHub`-Klasse erbt von der `Hub`-Klasse von SignalR.</span><span class="sxs-lookup"><span data-stu-id="61c71-270">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="61c71-271">Die `Hub`-Klasse verwaltet Verbindungen, Gruppen und Messaging.</span><span class="sxs-lookup"><span data-stu-id="61c71-271">The `Hub` class manages connections, groups, and messaging.</span></span>  

  <span data-ttu-id="61c71-272">Die `SendMessage`-Methode kann von einem verbundenen Client aufgerufen werden, um eine Nachricht an alle Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="61c71-272">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="61c71-273">JavaScript-Clientcode, in dem die Methode aufgerufen wird, wird später in diesem Tutorial vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="61c71-273">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="61c71-274">SignalR-Code ist asynchron, damit die maximale Skalierbarkeit gewährleistet werden kann.</span><span class="sxs-lookup"><span data-stu-id="61c71-274">SignalR code is asynchronous to provide maximum scalability.</span></span>    

## <a name="configure-no-locsignalr"></a><span data-ttu-id="61c71-275">Konfigurieren von SignalR</span><span class="sxs-lookup"><span data-stu-id="61c71-275">Configure SignalR</span></span>  

<span data-ttu-id="61c71-276">Der SignalR-Server muss so konfiguriert werden, dass er SignalR-Anforderungen an SignalR weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="61c71-276">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>    

* <span data-ttu-id="61c71-277">Fügen Sie der *Startup.cs*-Datei den folgenden hervorgehobenen Code zu.</span><span class="sxs-lookup"><span data-stu-id="61c71-277">Add the following highlighted code to the *Startup.cs* file.</span></span>  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  <span data-ttu-id="61c71-278">Durch diese Änderungen wird SignalR dem Abhängigkeitsinjektionssystem von ASP.NET Core sowie der Middlewarepipeline hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="61c71-278">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>  

## <a name="add-no-locsignalr-client-code"></a><span data-ttu-id="61c71-279">Hinzufügen von SignalR-Clientcode</span><span class="sxs-lookup"><span data-stu-id="61c71-279">Add SignalR client code</span></span>    

* <span data-ttu-id="61c71-280">Ersetzen Sie den Inhalt in *Pages\Index.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="61c71-280">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  <span data-ttu-id="61c71-281">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="61c71-281">The preceding code:</span></span>   

  * <span data-ttu-id="61c71-282">erstellt Textfelder für den Namen und den Nachrichtentext sowie eine Senden-Schaltfläche</span><span class="sxs-lookup"><span data-stu-id="61c71-282">Creates text boxes for name and message text, and a submit button.</span></span>  
  * <span data-ttu-id="61c71-283">erstellt eine Liste mit `id="messagesList"` zum Anzeigen von Nachrichten, die vom SignalR-Hub empfangen werden</span><span class="sxs-lookup"><span data-stu-id="61c71-283">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>   
  * <span data-ttu-id="61c71-284">schließt Skriptverweise auf SignalR sowie den *chat.js*-Anwendungscode ein, den Sie im folgenden Schritt erstellen</span><span class="sxs-lookup"><span data-stu-id="61c71-284">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>    

* <span data-ttu-id="61c71-285">Erstellen Sie im *wwwroot/js*-Ordner eine *chat.js*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="61c71-285">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  <span data-ttu-id="61c71-286">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="61c71-286">The preceding code:</span></span>   

  * <span data-ttu-id="61c71-287">erstellt und startet eine Verbindung</span><span class="sxs-lookup"><span data-stu-id="61c71-287">Creates and starts a connection.</span></span>    
  * <span data-ttu-id="61c71-288">fügt einen Handler zur Senden-Schaltfläche hinzu, der Nachrichten an den Hub sendet</span><span class="sxs-lookup"><span data-stu-id="61c71-288">Adds to the submit button a handler that sends messages to the hub.</span></span> 
  * <span data-ttu-id="61c71-289">fügt einen Handler zum Verbindungsobjekt hinzu, der Nachrichten vom Hub empfängt und diese der Liste hinzufügt</span><span class="sxs-lookup"><span data-stu-id="61c71-289">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>  

## <a name="run-the-app"></a><span data-ttu-id="61c71-290">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="61c71-290">Run the app</span></span>  

# <a name="visual-studio"></a>[<span data-ttu-id="61c71-291">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="61c71-291">Visual Studio</span></span>](#tab/visual-studio)   

* <span data-ttu-id="61c71-292">Drücken Sie **STRG+F5**, um die App ohne Debugging zu starten.</span><span class="sxs-lookup"><span data-stu-id="61c71-292">Press **CTRL+F5** to run the app without debugging.</span></span>   

# <a name="visual-studio-code"></a>[<span data-ttu-id="61c71-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="61c71-293">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="61c71-294">Führen Sie über das integrierte Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="61c71-294">In the integrated terminal, run the following command:</span></span>    

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="61c71-295">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="61c71-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="61c71-296">Wählen Sie im Menü **Ausführen > Starten ohne Debuggen** aus.</span><span class="sxs-lookup"><span data-stu-id="61c71-296">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="61c71-297">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="61c71-297">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="61c71-298">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Nachricht senden**.</span><span class="sxs-lookup"><span data-stu-id="61c71-298">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>  

  <span data-ttu-id="61c71-299">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="61c71-299">The name and message are displayed on both pages instantly.</span></span>   

  ![SignalR (Beispiel-App)](signalr/_static/2.x/signalr-get-started-finished.png) 

> [!TIP]    
> <span data-ttu-id="61c71-301">Wenn die App nicht funktioniert, öffnen Sie die Browser-Entwicklungstools (F12), und wechseln Sie zur Konsole.</span><span class="sxs-lookup"><span data-stu-id="61c71-301">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="61c71-302">Möglicherweise werden Fehler in Bezug auf Ihren HTML- und JavaScript-Code angezeigt.</span><span class="sxs-lookup"><span data-stu-id="61c71-302">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="61c71-303">Nehmen wir an, dass Sie z.B. *signalr.js* in einen anderen Ordner als vorgeschrieben platziert haben.</span><span class="sxs-lookup"><span data-stu-id="61c71-303">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="61c71-304">In diesem Fall funktioniert der Verweis auf diese Datei nicht, und Ihnen wird der Fehler 404 in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="61c71-304">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>   
> <span data-ttu-id="61c71-305">![Fehler „signalr.js nicht gefunden“](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="61c71-305">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>    
## <a name="additional-resources"></a><span data-ttu-id="61c71-306">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="61c71-306">Additional resources</span></span> 
* [<span data-ttu-id="61c71-307">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="61c71-307">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

::: moniker-end
