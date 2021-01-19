---
title: Docker-Images für ASP.NET Core
author: rick-anderson
description: In diesem Artikel erfahren Sie, wie Sie die veröffentlichten ASP.NET Core-Docker-Images aus der Docker-Registrierung verwenden. Außerdem erfahren Sie, wie Sie Ihre eigenen Images pullen und erstellen.
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2021
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
uid: host-and-deploy/docker/building-net-docker-images
ms.openlocfilehash: 2cd21722082af88e536bc1001b606ee96e7cf59b
ms.sourcegitcommit: b64c44ba5e3abb4ad4d50de93b7e282bf0f251e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972053"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="5e43c-104">Docker-Images für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e43c-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="5e43c-105">In diesem Tutorial wird gezeigt, wie eine ASP.NET Core-App in Docker-Containern ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5e43c-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="5e43c-106">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="5e43c-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="5e43c-107">erhalten Sie Informationen über ASP.NET Core-Docker-Images</span><span class="sxs-lookup"><span data-stu-id="5e43c-107">Learn about ASP.NET Core Docker images</span></span>
> * <span data-ttu-id="5e43c-108">Herunterladen einer ASP.NET Core-Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="5e43c-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="5e43c-109">Lokales Ausführen der Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="5e43c-109">Run the sample app locally</span></span>
> * <span data-ttu-id="5e43c-110">Ausführen der Beispiel-App in Linux-Containern</span><span class="sxs-lookup"><span data-stu-id="5e43c-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="5e43c-111">Ausführen der Beispiel-App in Windows-Containern</span><span class="sxs-lookup"><span data-stu-id="5e43c-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="5e43c-112">Manuelles Erstellen und Bereitstellen</span><span class="sxs-lookup"><span data-stu-id="5e43c-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="5e43c-113">ASP.NET Core Docker-Images</span><span class="sxs-lookup"><span data-stu-id="5e43c-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="5e43c-114">Für dieses Tutorial laden Sie eine ASP.NET Core-Beispiel-App herunter, und führen sie in Docker-Containern aus.</span><span class="sxs-lookup"><span data-stu-id="5e43c-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="5e43c-115">Das Beispiel funktioniert sowohl mit Linux- als auch mit Windows-Containern.</span><span class="sxs-lookup"><span data-stu-id="5e43c-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="5e43c-116">Die Dockerfile-Beispieldatei verwendet das [Docker-Feature für mehrstufige Builds](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) zur Erstellung und Ausführung in verschiedenen Containern.</span><span class="sxs-lookup"><span data-stu-id="5e43c-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="5e43c-117">Die Erstellungs- und Ausführungscontainer werden aus Images erstellt, die im Docker-Hub von Microsoft bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="5e43c-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  <span data-ttu-id="5e43c-118">Das Beispiel verwendet dieses Image für die Erstellung der App.</span><span class="sxs-lookup"><span data-stu-id="5e43c-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="5e43c-119">Das Image enthält das .NET SDK, das die Befehlszeilentools (CLI) enthält.</span><span class="sxs-lookup"><span data-stu-id="5e43c-119">The image contains the .NET SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="5e43c-120">Das Image ist für die lokale Entwicklung sowie für das Debuggen und für Komponententests optimiert.</span><span class="sxs-lookup"><span data-stu-id="5e43c-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="5e43c-121">Aufgrund der installierten Tools für die Entwicklung und Kompilierung ist das Image relativ groß.</span><span class="sxs-lookup"><span data-stu-id="5e43c-121">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  <span data-ttu-id="5e43c-122">Das Beispiel verwendet dieses Image für die Erstellung der App.</span><span class="sxs-lookup"><span data-stu-id="5e43c-122">The sample uses this image for building the app.</span></span> <span data-ttu-id="5e43c-123">Das Image enthält das .NET Core SDK mit den Befehlszeilentools (CLI).</span><span class="sxs-lookup"><span data-stu-id="5e43c-123">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="5e43c-124">Das Image ist für die lokale Entwicklung sowie für das Debuggen und für Komponententests optimiert.</span><span class="sxs-lookup"><span data-stu-id="5e43c-124">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="5e43c-125">Aufgrund der installierten Tools für die Entwicklung und Kompilierung ist das Image relativ groß.</span><span class="sxs-lookup"><span data-stu-id="5e43c-125">The tools installed for development and compilation make the image relatively large.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   <span data-ttu-id="5e43c-126">Das Beispiel verwendet dieses Image zum Ausführen der App.</span><span class="sxs-lookup"><span data-stu-id="5e43c-126">The sample uses this image for running the app.</span></span> <span data-ttu-id="5e43c-127">Das Image enthält die ASP.NET Core-Runtime und -Bibliotheken und wurde für das Ausführen von Apps in der Produktion optimiert.</span><span class="sxs-lookup"><span data-stu-id="5e43c-127">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="5e43c-128">Das Image wurde im Hinblick auf Bereitstellungs- und App-Startgeschwindigkeit entwickelt und ist daher relativ klein, sodass die Netzwerkleistung von Docker-Registrierung zu Docker-Host optimiert ist.</span><span class="sxs-lookup"><span data-stu-id="5e43c-128">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="5e43c-129">Nur die zum Ausführen einer App benötigten Binärdateien und Inhalte werden in den Container kopiert.</span><span class="sxs-lookup"><span data-stu-id="5e43c-129">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="5e43c-130">Die Inhalte sind bereit zur Ausführung und ermöglichen in kürzester Zeit nach dem `docker run` das Starten der App.</span><span class="sxs-lookup"><span data-stu-id="5e43c-130">The contents are ready to run, enabling the fastest time from `docker run` to app startup.</span></span> <span data-ttu-id="5e43c-131">Die dynamische Codekompilierung ist im Docker-Modell nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5e43c-131">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5e43c-132">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="5e43c-132">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="5e43c-133">.NET SDK 5.0</span><span class="sxs-lookup"><span data-stu-id="5e43c-133">.NET SDK 5.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [<span data-ttu-id="5e43c-134">.NET Core SDK 3.1</span><span class="sxs-lookup"><span data-stu-id="5e43c-134">.NET Core SDK 3.1</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="5e43c-135">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="5e43c-135">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* <span data-ttu-id="5e43c-136">Docker-Client 18.03 oder höher</span><span class="sxs-lookup"><span data-stu-id="5e43c-136">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="5e43c-137">Linux-Verteilungen</span><span class="sxs-lookup"><span data-stu-id="5e43c-137">Linux distributions</span></span>
    * [<span data-ttu-id="5e43c-138">CentOS</span><span class="sxs-lookup"><span data-stu-id="5e43c-138">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="5e43c-139">Debian</span><span class="sxs-lookup"><span data-stu-id="5e43c-139">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="5e43c-140">Fedora</span><span class="sxs-lookup"><span data-stu-id="5e43c-140">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="5e43c-141">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="5e43c-141">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="5e43c-142">macOS</span><span class="sxs-lookup"><span data-stu-id="5e43c-142">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="5e43c-143">Windows</span><span class="sxs-lookup"><span data-stu-id="5e43c-143">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="5e43c-144">Git</span><span class="sxs-lookup"><span data-stu-id="5e43c-144">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="5e43c-145">Herunterladen der Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="5e43c-145">Download the sample app</span></span>

* <span data-ttu-id="5e43c-146">Laden Sie das Beispiel herunter, indem Sie das [.NET Docker-Repository](https://github.com/dotnet/dotnet-docker) klonen:</span><span class="sxs-lookup"><span data-stu-id="5e43c-146">Download the sample by cloning the [.NET Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="5e43c-147">Lokales Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="5e43c-147">Run the app locally</span></span>

* <span data-ttu-id="5e43c-148">Navigieren Sie zum Projektordner unter *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="5e43c-148">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="5e43c-149">Führen Sie den folgenden Befehl aus, um die App zu erstellen und lokal auszuführen:</span><span class="sxs-lookup"><span data-stu-id="5e43c-149">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="5e43c-150">Navigieren Sie in einem Browser zu `http://localhost:5000`, um die App zu testen.</span><span class="sxs-lookup"><span data-stu-id="5e43c-150">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="5e43c-151">Drücken Sie Strg+C an der Eingabeaufforderung, um die App zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="5e43c-151">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="5e43c-152">Ausführen in einem Linux-Container</span><span class="sxs-lookup"><span data-stu-id="5e43c-152">Run in a Linux container</span></span>

* <span data-ttu-id="5e43c-153">Wechseln Sie im Docker-Client [zu Linux-Containern](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="5e43c-153">In the Docker client, [switch to Linux containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

* <span data-ttu-id="5e43c-154">Navigieren Sie zum Dockerfile-Ordner unter *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="5e43c-154">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="5e43c-155">Führen Sie die folgenden Befehle zum Erstellen und Ausführen des Beispiels in Docker aus:</span><span class="sxs-lookup"><span data-stu-id="5e43c-155">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="5e43c-156">Argumente des `build`-Befehls:</span><span class="sxs-lookup"><span data-stu-id="5e43c-156">The `build` command arguments:</span></span>
  * <span data-ttu-id="5e43c-157">Nennen Sie das Image „aspnetapp“.</span><span class="sxs-lookup"><span data-stu-id="5e43c-157">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="5e43c-158">Suchen Sie nach der Dockerfil-Datei im aktuellen Ordner (Punkt am Ende).</span><span class="sxs-lookup"><span data-stu-id="5e43c-158">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="5e43c-159">Argumente des run-Befehls:</span><span class="sxs-lookup"><span data-stu-id="5e43c-159">The run command arguments:</span></span>
  * <span data-ttu-id="5e43c-160">Ordnen Sie ein pseudo-TTY zu, und halten Sie es offen, wenn auch nicht angefügt.</span><span class="sxs-lookup"><span data-stu-id="5e43c-160">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="5e43c-161">(Dieselben Auswirkungen wie `--interactive --tty`.)</span><span class="sxs-lookup"><span data-stu-id="5e43c-161">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="5e43c-162">Entfernen Sie den Container, wenn er beendet wird.</span><span class="sxs-lookup"><span data-stu-id="5e43c-162">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="5e43c-163">Ordnen Sie Port 5000 auf dem lokalen Computer Port 80 im Container zu.</span><span class="sxs-lookup"><span data-stu-id="5e43c-163">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="5e43c-164">Nennen Sie den Container „aspnetcore_sample“.</span><span class="sxs-lookup"><span data-stu-id="5e43c-164">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="5e43c-165">Geben Sie das aspnetapp-Image an.</span><span class="sxs-lookup"><span data-stu-id="5e43c-165">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="5e43c-166">Navigieren Sie in einem Browser zu `http://localhost:5000`, um die App zu testen.</span><span class="sxs-lookup"><span data-stu-id="5e43c-166">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="5e43c-167">Ausführen in einem Windows-Container</span><span class="sxs-lookup"><span data-stu-id="5e43c-167">Run in a Windows container</span></span>

* <span data-ttu-id="5e43c-168">Wechseln Sie im Docker-Client [zu Windows-Containern](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span><span class="sxs-lookup"><span data-stu-id="5e43c-168">In the Docker client, [switch to Windows containers](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).</span></span>

<span data-ttu-id="5e43c-169">Navigieren Sie zum Dockerfile-Ordner unter `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="5e43c-169">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="5e43c-170">Führen Sie die folgenden Befehle zum Erstellen und Ausführen des Beispiels in Docker aus:</span><span class="sxs-lookup"><span data-stu-id="5e43c-170">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="5e43c-171">Für Windows-Container benötigen Sie die IP-Adresse des Containers (Navigieren zu `http://localhost:5000` funktioniert nicht):</span><span class="sxs-lookup"><span data-stu-id="5e43c-171">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="5e43c-172">Öffnen Sie eine weitere Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="5e43c-172">Open up another command prompt.</span></span>
  * <span data-ttu-id="5e43c-173">Führen Sie `docker ps` aus, um die ausgeführten Container anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5e43c-173">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="5e43c-174">Vergewissern Sie sich, dass der Container „aspnetcore_sample“ vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="5e43c-174">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="5e43c-175">Führen Sie `docker exec aspnetcore_sample ipconfig` aus, um die IP-Adresse des Containers anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5e43c-175">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="5e43c-176">Die Ausgabe des Befehls sieht wie im folgenden Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="5e43c-176">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="5e43c-177">Kopieren Sie die IPv4-Adresse des Containers (z.B. 172.29.245.43), und fügen Sie sie in die Adressleiste des Browsers ein, um die App zu testen.</span><span class="sxs-lookup"><span data-stu-id="5e43c-177">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="5e43c-178">Manuelles Erstellen und Bereitstellen</span><span class="sxs-lookup"><span data-stu-id="5e43c-178">Build and deploy manually</span></span>

<span data-ttu-id="5e43c-179">In einigen Szenarios müssen Sie möglicherweise eine App in einem Container bereitstellen, indem Sie die erforderlichen Ressourcen zur Laufzeit kopieren.</span><span class="sxs-lookup"><span data-stu-id="5e43c-179">In some scenarios, you might want to deploy an app to a container by copying its assets that are needed at run time.</span></span> <span data-ttu-id="5e43c-180">In diesem Abschnitt wird gezeigt, wie Sie die Bereitstellung manuell ausführen.</span><span class="sxs-lookup"><span data-stu-id="5e43c-180">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="5e43c-181">Navigieren Sie zum Projektordner unter *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="5e43c-181">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="5e43c-182">Führen Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) aus:</span><span class="sxs-lookup"><span data-stu-id="5e43c-182">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="5e43c-183">Argumente des Befehls:</span><span class="sxs-lookup"><span data-stu-id="5e43c-183">The command arguments:</span></span>
  * <span data-ttu-id="5e43c-184">erstellen die App im Releasemodus (der Debugmodus ist der Standardmodus).</span><span class="sxs-lookup"><span data-stu-id="5e43c-184">Build the app in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="5e43c-185">erstellen die Ressourcen im Ordner *published* (Veröffentlicht).</span><span class="sxs-lookup"><span data-stu-id="5e43c-185">Create the assets in the *published* folder.</span></span>

* <span data-ttu-id="5e43c-186">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="5e43c-186">Run the app.</span></span>

  * <span data-ttu-id="5e43c-187">Windows:</span><span class="sxs-lookup"><span data-stu-id="5e43c-187">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="5e43c-188">Linux:</span><span class="sxs-lookup"><span data-stu-id="5e43c-188">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="5e43c-189">Navigieren Sie zu `http://localhost:5000`, um die Startseite anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5e43c-189">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="5e43c-190">Erstellen Sie eine neue *Dockerfile*, und verwenden Sie den Befehl `docker build .` zum Erstellen des Containers, um die manuell veröffentlichte App in einem Docker-Container zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5e43c-190">To use the manually published app within a Docker container, create a new *Dockerfile* and use the `docker build .` command to build the container.</span></span>

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="5e43c-191">Die Dockerfile-Datei</span><span class="sxs-lookup"><span data-stu-id="5e43c-191">The Dockerfile</span></span>

<span data-ttu-id="5e43c-192">Dies ist die *Dockerfile*-Datei, die vom zuvor ausgeführten Befehl `docker build` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="5e43c-192">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="5e43c-193">Dabei wird `dotnet publish` auf die gleiche Weise wie in diesem Abschnitt zum Erstellen und Bereitstellen verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e43c-193">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
# https://hub.docker.com/_/microsoft-dotnet
FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build
WORKDIR /source

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /source/aspnetapp
RUN dotnet publish -c release -o /app --no-restore

# final stage/image
FROM mcr.microsoft.com/dotnet/aspnet:5.0
WORKDIR /app
COPY --from=build /app ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="5e43c-194">In der vorherigen *Dockerfile* werden die `*.csproj`-Dateien kopiert und in unterschiedlichen *Ebenen* wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="5e43c-194">In the preceding *Dockerfile*, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="5e43c-195">Wenn der Befehl `docker build` ein Image erstellt, wird ein integrierter Cache verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e43c-195">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="5e43c-196">Wenn sich die `*.csproj`-Dateien seit der letzten Ausführung des Befehls `docker build` nicht geändert haben, muss der `dotnet restore`-Befehl nicht noch mal ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5e43c-196">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="5e43c-197">Stattdessen wird der integrierte Cache für die entsprechende `dotnet restore`-Ebene verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e43c-197">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="5e43c-198">Weitere Informationen finden Sie unter [Best Practices für Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="5e43c-198">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="5e43c-199">Die Dockerfile-Datei</span><span class="sxs-lookup"><span data-stu-id="5e43c-199">The Dockerfile</span></span>

<span data-ttu-id="5e43c-200">Dies ist die *Dockerfile*-Datei, die vom zuvor ausgeführten Befehl `docker build` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="5e43c-200">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="5e43c-201">Dabei wird `dotnet publish` auf die gleiche Weise wie in diesem Abschnitt zum Erstellen und Bereitstellen verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e43c-201">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:3.0 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

<span data-ttu-id="5e43c-202">Wie bereits in der vorherigen Dockerfile-Datei erwähnt, werden `*.csproj`-Dateien kopiert und als unterschiedliche *Ebenen* wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="5e43c-202">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="5e43c-203">Wenn der Befehl `docker build` ein Image erstellt, wird ein integrierter Cache verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e43c-203">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="5e43c-204">Wenn sich die `*.csproj`-Dateien seit der letzten Ausführung des Befehls `docker build` nicht geändert haben, muss der `dotnet restore`-Befehl nicht noch mal ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5e43c-204">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="5e43c-205">Stattdessen wird der integrierte Cache für die entsprechende `dotnet restore`-Ebene verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e43c-205">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="5e43c-206">Weitere Informationen finden Sie unter [Best Practices für Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="5e43c-206">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="5e43c-207">Die Dockerfile-Datei</span><span class="sxs-lookup"><span data-stu-id="5e43c-207">The Dockerfile</span></span>

<span data-ttu-id="5e43c-208">Dies ist die *Dockerfile*-Datei, die vom zuvor ausgeführten Befehl `docker build` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="5e43c-208">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span> <span data-ttu-id="5e43c-209">Dabei wird `dotnet publish` auf die gleiche Weise wie in diesem Abschnitt zum Erstellen und Bereitstellen verwendet.</span><span class="sxs-lookup"><span data-stu-id="5e43c-209">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

```dockerfile
FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /app

# copy csproj and restore as distinct layers
COPY *.sln .
COPY aspnetapp/*.csproj ./aspnetapp/
RUN dotnet restore

# copy everything else and build app
COPY aspnetapp/. ./aspnetapp/
WORKDIR /app/aspnetapp
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY --from=build /app/aspnetapp/out ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5e43c-210">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5e43c-210">Additional resources</span></span>

* [<span data-ttu-id="5e43c-211">docker build-Befehl</span><span class="sxs-lookup"><span data-stu-id="5e43c-211">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="5e43c-212">docker run-Befehl</span><span class="sxs-lookup"><span data-stu-id="5e43c-212">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="5e43c-213">[Beispiel für ASP.NET Core-Docker](https://github.com/dotnet/dotnet-docker) (das in diesem Tutorial verwendete Beispiel)</span><span class="sxs-lookup"><span data-stu-id="5e43c-213">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="5e43c-214">Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="5e43c-214">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="5e43c-215">Arbeiten mit Visual Studio-Tools für Docker</span><span class="sxs-lookup"><span data-stu-id="5e43c-215">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="5e43c-216">Debuggen mit Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5e43c-216">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="5e43c-217">GC mittels Docker und kleiner Containers</span><span class="sxs-lookup"><span data-stu-id="5e43c-217">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="5e43c-218">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5e43c-218">Next steps</span></span>

<span data-ttu-id="5e43c-219">Im Git-Repository, das die Beispiel-App enthält, finden Sie auch die Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="5e43c-219">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="5e43c-220">Eine Übersicht über die im Repository verfügbaren Ressourcen finden Sie in der [Infodatei](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="5e43c-220">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="5e43c-221">Insbesondere erfahren Sie, wie Sie HTTPS implementieren:</span><span class="sxs-lookup"><span data-stu-id="5e43c-221">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="5e43c-222">Entwickeln von ASP.NET Core-Anwendungen mit Docker über HTTPS</span><span class="sxs-lookup"><span data-stu-id="5e43c-222">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
