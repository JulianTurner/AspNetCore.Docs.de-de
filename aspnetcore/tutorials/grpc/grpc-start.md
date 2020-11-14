---
title: Erstellen eines .NET Core-gRPC-Clients und -Servers in ASP.NET Core
author: juntaoluo
description: In diesem Tutorial erfahren Sie, wie Sie einen gRPC-Dienst und -Client in ASP.NET Core erstellen können. Dabei erfahren Sie, wie Sie ein gRPC-Dienstprojekt erstellen, eine PROTO-Datei bearbeiten und einen Duplexstreamaufruf hinzufügen.
ms.author: johluo
ms.date: 10/23/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/grpc/grpc-start
ms.openlocfilehash: 9388a2f814008ebb50171f85b8baccf6dadfac27
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057023"
---
# <a name="tutorial-create-a-grpc-client-and-server-in-aspnet-core"></a><span data-ttu-id="6a6d9-104">Tutorial: Erstellen eines gRPC-Clients und -Servers in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a6d9-104">Tutorial: Create a gRPC client and server in ASP.NET Core</span></span>

<span data-ttu-id="6a6d9-105">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="6a6d9-105">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="6a6d9-106">In diesem Tutorial erfahren Sie, wie Sie einen .NET Core-[gRPC](https://grpc.io/docs/guides/)-Client und einen ASP.NET Core-gRPC-Server erstellen können.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-106">This tutorial shows how to create a .NET Core [gRPC](https://grpc.io/docs/guides/) client and an ASP.NET Core gRPC Server.</span></span>

<span data-ttu-id="6a6d9-107">Das Ziel ist ein gRPC-Client, der mit dem gRPC-Greeter-Dienst kommuniziert.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-107">At the end, you'll have a gRPC client that communicates with the gRPC Greeter service.</span></span>

<span data-ttu-id="6a6d9-108">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6a6d9-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6a6d9-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6a6d9-110">Erstellen Sie einen gRPC-Server.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-110">Create a gRPC Server.</span></span>
> * <span data-ttu-id="6a6d9-111">Erstellen Sie einen gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-111">Create a gRPC client.</span></span>
> * <span data-ttu-id="6a6d9-112">Testen Sie den gRPC-Clientdienst mit dem gRPC-Greeterdienst (Begrüßungsdienst).</span><span class="sxs-lookup"><span data-stu-id="6a6d9-112">Test the gRPC client service with the gRPC Greeter service.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a6d9-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="6a6d9-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a6d9-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a6d9-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a6d9-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a6d9-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a6d9-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="6a6d9-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="6a6d9-117">Visual Studio für Mac ab Version 8.7</span><span class="sxs-lookup"><span data-stu-id="6a6d9-117">Visual Studio for Mac version 8.7 or later</span></span>](/visualstudio/releasenotes/vs2019-mac-relnotes)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]
---

## <a name="create-a-grpc-service"></a><span data-ttu-id="6a6d9-118">Erstellen eines gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="6a6d9-118">Create a gRPC service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a6d9-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a6d9-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6a6d9-120">Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-120">Start Visual Studio and select **Create a new project**.</span></span> <span data-ttu-id="6a6d9-121">Alternativ dazu können Sie auch im Visual Studio-Menü **Datei** auf **Neu** > **Projekt** klicken.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-121">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6a6d9-122">Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **gRPC-Dienst** aus, und klicken Sie auf **Weiter** :</span><span class="sxs-lookup"><span data-stu-id="6a6d9-122">In the **Create a new project** dialog, select **gRPC Service** and select **Next** :</span></span>

  ![Dialogfeld „Neues Projekt erstellen“ in Visual Studio](~/tutorials/grpc/grpc-start/static/cnp.png)

* <span data-ttu-id="6a6d9-124">Nennen Sie das Projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-124">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="6a6d9-125">Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-125">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="6a6d9-126">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-126">Select **Create**.</span></span>
* <span data-ttu-id="6a6d9-127">Im Dialogfeld **Neuen gRPC-Dienst erstellen** :</span><span class="sxs-lookup"><span data-stu-id="6a6d9-127">In the **Create a new gRPC service** dialog:</span></span>
  * <span data-ttu-id="6a6d9-128">Die **gRPC-Dienst** ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-128">The **gRPC Service** template is selected.</span></span>
  * <span data-ttu-id="6a6d9-129">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-129">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a6d9-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a6d9-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6a6d9-131">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6a6d9-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6a6d9-132">Ändern Sie die Verzeichnisse (mit `cd`) in einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-132">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="6a6d9-133">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new grpc -o GrpcGreeter
  code -r GrpcGreeter
  ```

  * <span data-ttu-id="6a6d9-134">Mit dem Befehl `dotnet new` wird ein neuer gRPC-Dienst im Ordner *GrpcGreeter* erstellt.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-134">The `dotnet new` command creates a new gRPC service in the *GrpcGreeter* folder.</span></span>
  * <span data-ttu-id="6a6d9-135">Mit dem Befehl `code` wird der Ordner *GrpcGreeter* in einer neuen Instanz von Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-135">The `code` command opens the *GrpcGreeter* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="6a6d9-136">Ein Dialogfeld mit folgender Meldung wird angezeigt: **Required assets to build and debug are missing from 'GrpcGreeter'. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="6a6d9-136">A dialog box appears with **Required assets to build and debug are missing from 'GrpcGreeter'. Add them?**</span></span>
* <span data-ttu-id="6a6d9-137">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-137">Select **Yes**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a6d9-138">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="6a6d9-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6a6d9-139">Starten Sie Visual Studio für Mac, und wählen Sie **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-139">Start Visual Studio for Mac and select **Create a new project**.</span></span> <span data-ttu-id="6a6d9-140">Alternativ dazu können Sie auch im Visual Studio-Menü **Datei** auf **Neu** > **Projekt** klicken.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-140">Alternatively, from the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="6a6d9-141">Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **Web und Konsole** > **App** > **gRPC-Dienst** und dann **Weiter** aus:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-141">In the **Create a new project** dialog, select **Web and Console** > **App** > **gRPC Service** and select **Next** :</span></span>

  ![Dialogfeld „Neues Projekt erstellen“ unter macOS](~/tutorials/grpc/grpc-start/static/cnp-mac.png)

* <span data-ttu-id="6a6d9-143">Wählen Sie **.NET Core 3.1** als Zielframework aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-143">Select **.NET Core 3.1** for the target framework and select **Next**.</span></span>
* <span data-ttu-id="6a6d9-144">Nennen Sie das Projekt **GrpcGreeter**.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-144">Name the project **GrpcGreeter**.</span></span> <span data-ttu-id="6a6d9-145">Es ist wichtig, den Namen *GrpcGreeter* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-145">It's important to name the project *GrpcGreeter* so the namespaces match when you copy and paste code.</span></span>
* <span data-ttu-id="6a6d9-146">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-146">Select **Create**.</span></span>
---

### <a name="run-the-service"></a><span data-ttu-id="6a6d9-147">Ausführen des Diensts</span><span class="sxs-lookup"><span data-stu-id="6a6d9-147">Run the service</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

<span data-ttu-id="6a6d9-148">In den Protokollen wird vermerkt, dass der Dienst auf `https://localhost:5001` lauscht.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-148">The logs show the service listening on `https://localhost:5001`.</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

> [!NOTE]
> <span data-ttu-id="6a6d9-149">Die gRPC-Vorlage ist für die Verwendung von [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-149">The gRPC template is configured to use [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="6a6d9-150">gRPC-Clients müssen zum Aufrufen des Servers HTTPS verwenden.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-150">gRPC clients need to use HTTPS to call the server.</span></span>
>
> <span data-ttu-id="6a6d9-151">macOS unterstützt ASP.NET Core gRPC mit TLS nicht.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-151">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="6a6d9-152">Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-152">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="6a6d9-153">Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="6a6d9-153">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

### <a name="examine-the-project-files"></a><span data-ttu-id="6a6d9-154">Überprüfen der Projektdateien</span><span class="sxs-lookup"><span data-stu-id="6a6d9-154">Examine the project files</span></span>

<span data-ttu-id="6a6d9-155">*GrpcGreeter* -Projektdateien:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-155">*GrpcGreeter* project files:</span></span>

* <span data-ttu-id="6a6d9-156">*greet.proto* : Mit der Datei *Protos/greet.proto* werden der `Greeter`-gRPC-Dienst definiert und die gRPC-Serverobjekte generiert.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-156">*greet.proto* : The *Protos/greet.proto* file defines the `Greeter` gRPC and is used to generate the gRPC server assets.</span></span> <span data-ttu-id="6a6d9-157">Weitere Informationen finden Sie unter [Einführung in gRPC in ASP.NET Core](xref:grpc/index).</span><span class="sxs-lookup"><span data-stu-id="6a6d9-157">For more information, see [Introduction to gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="6a6d9-158">*Services* -Ordner: Dieser enthält die Implementierung des `Greeter`-Diensts.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-158">*Services* folder: Contains the implementation of the `Greeter` service.</span></span>
* <span data-ttu-id="6a6d9-159">*appSettings.json* : Diese Datei enthält Konfigurationsdaten wie z. B. das von Kestrel verwendete Protokoll.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-159">*appSettings.json* : Contains configuration data, such as protocol used by Kestrel.</span></span> <span data-ttu-id="6a6d9-160">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-160">For more information, see <xref:fundamentals/configuration/index>.</span></span>
* <span data-ttu-id="6a6d9-161">*Program.cs* : Diese Datei enthält den Einstiegspunkt für den gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-161">*Program.cs* : Contains the entry point for the gRPC service.</span></span> <span data-ttu-id="6a6d9-162">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-162">For more information, see <xref:fundamentals/host/generic-host>.</span></span>
* <span data-ttu-id="6a6d9-163">*Startup.cs* : Diese Datei enthält Code, mit dem das App-Verhalten konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-163">*Startup.cs* : Contains code that configures app behavior.</span></span> <span data-ttu-id="6a6d9-164">Weitere Informationen finden Sie unter [App-Start](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="6a6d9-164">For more information, see [App startup](xref:fundamentals/startup).</span></span>

## <a name="create-the-grpc-client-in-a-net-console-app"></a><span data-ttu-id="6a6d9-165">Erstellen des gRPC-Clients in einer .NET-Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="6a6d9-165">Create the gRPC client in a .NET console app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a6d9-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a6d9-166">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6a6d9-167">Öffnen Sie eine zweite Instanz von Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-167">Open a second instance of Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="6a6d9-168">Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **Konsolen-App (.NET Core)** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-168">In the **Create a new project** dialog, select **Console App (.NET Core)** and select **Next**.</span></span>
* <span data-ttu-id="6a6d9-169">Geben Sie im Textfeld **Projektname** den Namen **GrpcGreeterClient** ein, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-169">In the **Project name** text box, enter **GrpcGreeterClient** and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a6d9-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a6d9-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6a6d9-171">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="6a6d9-171">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="6a6d9-172">Ändern Sie die Verzeichnisse (mit `cd`) in einen Ordner für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-172">Change directories (`cd`) to a folder for the project.</span></span>
* <span data-ttu-id="6a6d9-173">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-173">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new console -o GrpcGreeterClient
  code -r GrpcGreeterClient
  ```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a6d9-174">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="6a6d9-174">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="6a6d9-175">Befolgen Sie die Anweisungen unter [Erstellen einer vollständigen .NET Core-Lösung unter macOS mit Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution), um eine Konsolen-App namens *GrpcGreeterClient* zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-175">Follow the instructions in [Building a complete .NET Core solution on macOS using Visual Studio for Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution) to create a console app with the name *GrpcGreeterClient*.</span></span>

---

### <a name="add-required-packages"></a><span data-ttu-id="6a6d9-176">Hinzufügen von erforderlichen Paketen</span><span class="sxs-lookup"><span data-stu-id="6a6d9-176">Add required packages</span></span>

<span data-ttu-id="6a6d9-177">Das gRPC-Clientprojekt erfordert die folgenden Pakete:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-177">The gRPC client project requires the following packages:</span></span>

* <span data-ttu-id="6a6d9-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), das den .NET Core-Client enthält.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-178">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), which contains the .NET Core client.</span></span>
* <span data-ttu-id="6a6d9-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), das die Protobuf-Nachrichten-APIs für C# enthält.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-179">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/), which contains protobuf message APIs for C#.</span></span>
* <span data-ttu-id="6a6d9-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), das C#-Toolunterstützung für Protobuf-Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-180">[Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/), which contains C# tooling support for protobuf files.</span></span> <span data-ttu-id="6a6d9-181">Das Toolpaket ist nicht zur Laufzeit erforderlich, darum ist die Abhängigkeit mit `PrivateAssets="All"` markiert.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-181">The tooling package isn't required at runtime, so the dependency is marked with `PrivateAssets="All"`.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a6d9-182">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a6d9-182">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6a6d9-183">Installieren Sie die Pakete über die Paket-Manager-Konsole oder über „NuGet-Pakete verwalten“.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-183">Install the packages using either the Package Manager Console (PMC) or Manage NuGet Packages.</span></span>

#### <a name="pmc-option-to-install-packages"></a><span data-ttu-id="6a6d9-184">PMC-Option zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="6a6d9-184">PMC option to install packages</span></span>

* <span data-ttu-id="6a6d9-185">Wählen Sie in Visual Studio **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-185">From Visual Studio, select **Tools** > **NuGet Package Manager** > **Package Manager Console**</span></span>
* <span data-ttu-id="6a6d9-186">Führen Sie im Fenster **Paket-Manager-Konsole** den Befehl `cd GrpcGreeterClient` aus, um zu dem Verzeichnis zu wechseln, in dem sich die *GrpcGreeterClient.csproj* -Dateien befinden.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-186">From the **Package Manager Console** window, run `cd GrpcGreeterClient` to change directories to the folder containing the *GrpcGreeterClient.csproj* files.</span></span>
* <span data-ttu-id="6a6d9-187">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-187">Run the following commands:</span></span>

  ```powershell
  Install-Package Grpc.Net.Client
  Install-Package Google.Protobuf
  Install-Package Grpc.Tools
  ```

#### <a name="manage-nuget-packages-option-to-install-packages"></a><span data-ttu-id="6a6d9-188">Option „NuGet-Pakete verwalten“ zum Installieren von Paketen</span><span class="sxs-lookup"><span data-stu-id="6a6d9-188">Manage NuGet Packages option to install packages</span></span>

* <span data-ttu-id="6a6d9-189">Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-189">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="6a6d9-190">Wählen Sie die Registerkarte **Durchsuchen** aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-190">Select the **Browse** tab.</span></span>
* <span data-ttu-id="6a6d9-191">Geben Sie **Grpc.Net.Client** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-191">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="6a6d9-192">Wählen Sie das Paket **Grpc.Net.Client** auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-192">Select the **Grpc.Net.Client** package from the **Browse** tab and select **Install**.</span></span>
* <span data-ttu-id="6a6d9-193">Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-193">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a6d9-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a6d9-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="6a6d9-195">Führen Sie die folgenden Befehle über das **integrierte Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-195">Run the following commands from the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add GrpcGreeterClient.csproj package Grpc.Net.Client
dotnet add GrpcGreeterClient.csproj package Google.Protobuf
dotnet add GrpcGreeterClient.csproj package Grpc.Tools
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a6d9-196">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="6a6d9-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6a6d9-197">Klicken Sie im **Lösungspad** mit der rechten Maustaste auf das Projekt **GrpcGreeterClient** , und klicken Sie auf **Manage NuGet Packages** (NuGet-Pakete verwalten).</span><span class="sxs-lookup"><span data-stu-id="6a6d9-197">Right-click **GrpcGreeterClient** project in the **Solution Pad** and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="6a6d9-198">Geben Sie **Grpc.Net.Client** in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-198">Enter **Grpc.Net.Client** in the search box.</span></span>
* <span data-ttu-id="6a6d9-199">Wählen Sie das Paket **Grpc.Net.Client** im Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-199">Select the **Grpc.Net.Client** package from the results pane and select **Add Package**.</span></span>
* <span data-ttu-id="6a6d9-200">Klicken Sie im Dialogfeld **Accept License** (Lizenzbedingungen annehmen) auf **Akzeptieren**.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-200">Select the **Accept** button on the **Accept License** dialog.</span></span>
* <span data-ttu-id="6a6d9-201">Führen Sie diese Aktionen auch für `Google.Protobuf` und `Grpc.Tools` durch.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-201">Repeat for `Google.Protobuf` and `Grpc.Tools`.</span></span>

---

### <a name="add-greetproto"></a><span data-ttu-id="6a6d9-202">Fügen Sie „greet.proto“ hinzu:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-202">Add greet.proto</span></span>

* <span data-ttu-id="6a6d9-203">Erstellen Sie einen *Protos* -Ordner im gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-203">Create a *Protos* folder in the gRPC client project.</span></span>
* <span data-ttu-id="6a6d9-204">Kopieren Sie die Datei *Protos\greet.proto* aus dem gRPC-Greeter-Dienst in das gRPC-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-204">Copy the *Protos\greet.proto* file from the gRPC Greeter service to the gRPC client project.</span></span>
* <span data-ttu-id="6a6d9-205">Ändern Sie den Namespace in der Datei `greet.proto` in den Namespace des Projekts:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-205">Update the namespace inside the `greet.proto` file to the project's namespace:</span></span>

  ```
  option csharp_namespace = "GrpcGreeterClient";
  ```

* <span data-ttu-id="6a6d9-206">Bearbeiten Sie die Projektdatei *GrpcGreeterClient.csproj* :</span><span class="sxs-lookup"><span data-stu-id="6a6d9-206">Edit the *GrpcGreeterClient.csproj* project file:</span></span>

  # <a name="visual-studio"></a>[<span data-ttu-id="6a6d9-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a6d9-207">Visual Studio</span></span>](#tab/visual-studio)

  <span data-ttu-id="6a6d9-208">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Projektdatei bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-208">Right-click the project and select **Edit Project File**.</span></span>

  # <a name="visual-studio-code"></a>[<span data-ttu-id="6a6d9-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a6d9-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

  <span data-ttu-id="6a6d9-210">Wählen Sie die Datei *GrpcGreeterClient.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-210">Select the *GrpcGreeterClient.csproj* file.</span></span>

  # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a6d9-211">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="6a6d9-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  <span data-ttu-id="6a6d9-212">Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Projektdatei bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-212">Right-click the project and select **Edit Project File**.</span></span>

  ---

* <span data-ttu-id="6a6d9-213">Fügen Sie eine Elementgruppe mit einem `<Protobuf>`-Element hinzu, das auf die Datei *greet.proto* verweist:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-213">Add an item group with a `<Protobuf>` element that refers to the *greet.proto* file:</span></span>

  ```xml
  <ItemGroup>
    <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
  </ItemGroup>
  ```

### <a name="create-the-greeter-client"></a><span data-ttu-id="6a6d9-214">Erstellen des Greeter-Clients</span><span class="sxs-lookup"><span data-stu-id="6a6d9-214">Create the Greeter client</span></span>

<span data-ttu-id="6a6d9-215">Erstellen Sie das Clientprojekt, um die Typen im `GrpcGreeter`-Namespace zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-215">Build the client project to create the types in the `GrpcGreeter` namespace.</span></span> <span data-ttu-id="6a6d9-216">Die `GrpcGreeter`-Typen werden vom Buildprozess automatisch erstellt.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-216">The `GrpcGreeter` types are generated automatically by the build process.</span></span>

<span data-ttu-id="6a6d9-217">Aktualisieren Sie die Datei *Program.cs* des gRPC-Clients mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-217">Update the gRPC client *Program.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet2)]

<span data-ttu-id="6a6d9-218">*Program.cs* enthält den Einstiegspunkt und die Logik für den gRPC-Client.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-218">*Program.cs* contains the entry point and logic for the gRPC client.</span></span>

<span data-ttu-id="6a6d9-219">Der Greeter-Client wird folgendermaßen erstellt:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-219">The Greeter client is created by:</span></span>

* <span data-ttu-id="6a6d9-220">Instanziieren eines `GrpcChannel`-Objekts, das Informationen zum Herstellen einer Verbindung mit dem gRPC-Dienst enthält</span><span class="sxs-lookup"><span data-stu-id="6a6d9-220">Instantiating a `GrpcChannel` containing the information for creating the connection to the gRPC service.</span></span>
* <span data-ttu-id="6a6d9-221">Verwenden von `GrpcChannel` zum Erstellen des Greeter-Clients:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-221">Using the `GrpcChannel` to construct the Greeter client:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=3-5)]

<span data-ttu-id="6a6d9-222">Der Greeter-Client ruft die asynchrone Methode `SayHello` auf.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-222">The Greeter client calls the asynchronous `SayHello` method.</span></span> <span data-ttu-id="6a6d9-223">Das Ergebnis des Aufrufs von `SayHello` wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-223">The result of the `SayHello` call is displayed:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeterClient/Program.cs?name=snippet&highlight=6-8)]

## <a name="test-the-grpc-client-with-the-grpc-greeter-service"></a><span data-ttu-id="6a6d9-224">Testen des gRPC-Clients mit dem gRPC-Greeter-Dienst</span><span class="sxs-lookup"><span data-stu-id="6a6d9-224">Test the gRPC client with the gRPC Greeter service</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a6d9-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a6d9-225">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6a6d9-226">Drücken Sie im Greeterdienst `Ctrl+F5`, um den Server ohne Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-226">In the Greeter service, press `Ctrl+F5` to start the server without the debugger.</span></span>
* <span data-ttu-id="6a6d9-227">Drücken Sie im `GrpcGreeterClient`-Projekt `Ctrl+F5`, um den Client ohne den Debugger zu starten.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-227">In the `GrpcGreeterClient` project, press `Ctrl+F5` to start the client without the debugger.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="6a6d9-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="6a6d9-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="6a6d9-229">Starten Sie den Greeterdienst.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-229">Start the Greeter service.</span></span>
* <span data-ttu-id="6a6d9-230">Starten Sie den Client.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-230">Start the client.</span></span>


# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a6d9-231">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="6a6d9-231">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="6a6d9-232">Aufgrund der bereits erwähnten [Umgehung für das HTTP/2-TLS-Problem unter macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos) müssen Sie die Kanaladresse im Client in http://localhost:5000 ändern.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-232">Due to the previously mentioned [HTTP/2 TLS issue on macOS workaround](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos), you'll need to update the channel address in the client to "http://localhost:5000".</span></span> <span data-ttu-id="6a6d9-233">Ändern Sie Zeile 13 in **GrpcGreeterClient/Program.cs** folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-233">Update line 13 of **GrpcGreeterClient/Program.cs** to read:</span></span>
  ```csharp
  using var channel = GrpcChannel.ForAddress("http://localhost:5000");
  ``` 
* <span data-ttu-id="6a6d9-234">Starten Sie den Greeterdienst.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-234">Start the Greeter service.</span></span>
* <span data-ttu-id="6a6d9-235">Starten Sie den Client.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-235">Start the client.</span></span>

---

<span data-ttu-id="6a6d9-236">Der Client sendet eine Begrüßungsnachricht an den Dienst, die den Namen des Diensts enthält: *GreeterClient*.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-236">The client sends a greeting to the service with a message containing its name, *GreeterClient*.</span></span> <span data-ttu-id="6a6d9-237">Der Dienst gibt als Antwort die Meldung „Hello GreeterClient“ aus.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-237">The service sends the message "Hello GreeterClient" as a response.</span></span> <span data-ttu-id="6a6d9-238">Die Antwort „Hello GreeterClient“ wird in der Eingabeaufforderung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-238">The "Hello GreeterClient" response is displayed in the command prompt:</span></span>

```console
Greeting: Hello GreeterClient
Press any key to exit...
```

<span data-ttu-id="6a6d9-239">Der gRPC-Dienst zeichnet die Details des erfolgreichen Aufrufs in den Protokollen auf, die an die Eingabeaufforderung ausgegeben werden:</span><span class="sxs-lookup"><span data-stu-id="6a6d9-239">The gRPC service records the details of the successful call in the logs written to the command prompt:</span></span>

```console
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
info: Microsoft.Hosting.Lifetime[0]
      Content root path: C:\GH\aspnet\docs\4\Docs\aspnetcore\tutorials\grpc\grpc-start\sample\GrpcGreeter
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 78.32260000000001ms 200 application/grpc
```

> [!NOTE]
> <span data-ttu-id="6a6d9-240">Der Code in diesem Artikel erfordert das ASP.NET Core-HTTPS-Entwicklungszertifikat, um den gRPC-Dienst zu sichern.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-240">The code in this article requires the ASP.NET Core HTTPS development certificate to secure the gRPC service.</span></span> <span data-ttu-id="6a6d9-241">Wenn auf dem .NET gRPC-Client ein Fehler mit der Meldung `The remote certificate is invalid according to the validation procedure.` oder `The SSL connection could not be established.` auftritt, wird das Entwicklungszertifikat als nicht vertrauenswürdig eingestuft.</span><span class="sxs-lookup"><span data-stu-id="6a6d9-241">If the .NET gRPC client fails with the message `The remote certificate is invalid according to the validation procedure.` or `The SSL connection could not be established.`, the development certificate isn't trusted.</span></span> <span data-ttu-id="6a6d9-242">Informationen zum Beheben dieses Problems finden Sie unter [Aufrufen eines gRPC-Diensts mit einem nicht vertrauenswürdigen/ungültigen Zertifikat](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span><span class="sxs-lookup"><span data-stu-id="6a6d9-242">To fix this issue, see [Call a gRPC service with an untrusted/invalid certificate](xref:grpc/troubleshoot#call-a-grpc-service-with-an-untrustedinvalid-certificate).</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

### <a name="next-steps"></a><span data-ttu-id="6a6d9-243">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="6a6d9-243">Next steps</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
