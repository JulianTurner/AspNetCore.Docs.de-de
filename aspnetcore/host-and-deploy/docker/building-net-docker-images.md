---
title: Docker-Images für ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie veröffentlichte .NET Core-Docker-Images aus der Docker-Registrierung verwenden können. Übertragen Sie Images mithilfe von Pull, und erstellen Sie eigene Images.
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2020
no-loc:
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
ms.openlocfilehash: 791cc14feac30ad8a571d155624ab821dfd77981
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722857"
---
# <a name="docker-images-for-aspnet-core"></a><span data-ttu-id="24396-104">Docker-Images für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="24396-104">Docker images for ASP.NET Core</span></span>

<span data-ttu-id="24396-105">In diesem Tutorial wird gezeigt, wie eine ASP.NET Core-App in Docker-Containern ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="24396-105">This tutorial shows how to run an ASP.NET Core app in Docker containers.</span></span>

<span data-ttu-id="24396-106">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="24396-106">In this tutorial, you:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="24396-107">Allgemeines zu Docker-Images in Microsoft .NET Core</span><span class="sxs-lookup"><span data-stu-id="24396-107">Learn about Microsoft .NET Core Docker images</span></span>
> * <span data-ttu-id="24396-108">Herunterladen einer ASP.NET Core-Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="24396-108">Download an ASP.NET Core sample app</span></span>
> * <span data-ttu-id="24396-109">Lokales Ausführen der Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="24396-109">Run the sample app locally</span></span>
> * <span data-ttu-id="24396-110">Ausführen der Beispiel-App in Linux-Containern</span><span class="sxs-lookup"><span data-stu-id="24396-110">Run the sample app in Linux containers</span></span>
> * <span data-ttu-id="24396-111">Ausführen der Beispiel-App in Windows-Containern</span><span class="sxs-lookup"><span data-stu-id="24396-111">Run the sample app in Windows containers</span></span>
> * <span data-ttu-id="24396-112">Manuelles Erstellen und Bereitstellen</span><span class="sxs-lookup"><span data-stu-id="24396-112">Build and deploy manually</span></span>

## <a name="aspnet-core-docker-images"></a><span data-ttu-id="24396-113">ASP.NET Core Docker-Images</span><span class="sxs-lookup"><span data-stu-id="24396-113">ASP.NET Core Docker images</span></span>

<span data-ttu-id="24396-114">Für dieses Tutorial laden Sie eine ASP.NET Core-Beispiel-App herunter, und führen sie in Docker-Containern aus.</span><span class="sxs-lookup"><span data-stu-id="24396-114">For this tutorial, you download an ASP.NET Core sample app and run it in Docker containers.</span></span> <span data-ttu-id="24396-115">Das Beispiel funktioniert sowohl mit Linux- als auch mit Windows-Containern.</span><span class="sxs-lookup"><span data-stu-id="24396-115">The sample works with both Linux and Windows containers.</span></span>

<span data-ttu-id="24396-116">Die Dockerfile-Beispieldatei verwendet das [Docker-Feature für mehrstufige Builds](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) zur Erstellung und Ausführung in verschiedenen Containern.</span><span class="sxs-lookup"><span data-stu-id="24396-116">The sample Dockerfile uses the [Docker multi-stage build feature](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) to build and run in different containers.</span></span> <span data-ttu-id="24396-117">Die Erstellungs- und Ausführungscontainer werden aus Images erstellt, die im Docker-Hub von Microsoft bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="24396-117">The build and run containers are created from images that are provided in Docker Hub by Microsoft:</span></span>

* `dotnet/core/sdk`

  <span data-ttu-id="24396-118">Das Beispiel verwendet dieses Image für die Erstellung der App.</span><span class="sxs-lookup"><span data-stu-id="24396-118">The sample uses this image for building the app.</span></span> <span data-ttu-id="24396-119">Das Image enthält das .NET Core SDK mit den Befehlszeilentools (CLI).</span><span class="sxs-lookup"><span data-stu-id="24396-119">The image contains the .NET Core SDK, which includes the Command Line Tools (CLI).</span></span> <span data-ttu-id="24396-120">Das Image ist für die lokale Entwicklung sowie für das Debuggen und für Komponententests optimiert.</span><span class="sxs-lookup"><span data-stu-id="24396-120">The image is optimized for local development, debugging, and unit testing.</span></span> <span data-ttu-id="24396-121">Die für Entwicklung und Kompilierung installierten Tools machen dies zu einem relativ großen Image.</span><span class="sxs-lookup"><span data-stu-id="24396-121">The tools installed for development and compilation make this a relatively large image.</span></span> 

* `dotnet/core/aspnet`

   <span data-ttu-id="24396-122">Das Beispiel verwendet dieses Image zum Ausführen der App.</span><span class="sxs-lookup"><span data-stu-id="24396-122">The sample uses this image for running the app.</span></span> <span data-ttu-id="24396-123">Das Image enthält die ASP.NET Core-Runtime und -Bibliotheken und wurde für das Ausführen von Apps in der Produktion optimiert.</span><span class="sxs-lookup"><span data-stu-id="24396-123">The image contains the ASP.NET Core runtime and libraries and is optimized for running apps in production.</span></span> <span data-ttu-id="24396-124">Das Image wurde im Hinblick auf Bereitstellungs- und App-Startgeschwindigkeit entwickelt und ist daher relativ klein, sodass die Netzwerkleistung von Docker-Registrierung zu Docker-Host optimiert ist.</span><span class="sxs-lookup"><span data-stu-id="24396-124">Designed for speed of deployment and app startup, the image is relatively small, so network performance from Docker Registry to Docker host is optimized.</span></span> <span data-ttu-id="24396-125">Nur die zum Ausführen einer App benötigten Binärdateien und Inhalte werden in den Container kopiert.</span><span class="sxs-lookup"><span data-stu-id="24396-125">Only the binaries and content needed to run an app are copied to the container.</span></span> <span data-ttu-id="24396-126">Die Inhalte sind bereit zur Ausführung und ermöglichen in kürzester Zeit nach dem `Docker run` das Starten der App.</span><span class="sxs-lookup"><span data-stu-id="24396-126">The contents are ready to run, enabling the fastest time from `Docker run` to app startup.</span></span> <span data-ttu-id="24396-127">Die dynamische Codekompilierung ist im Docker-Modell nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="24396-127">Dynamic code compilation isn't needed in the Docker model.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="24396-128">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="24396-128">Prerequisites</span></span>
::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="24396-129">.NET Core 2.2 SDK</span><span class="sxs-lookup"><span data-stu-id="24396-129">.NET Core 2.2 SDK</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [<span data-ttu-id="24396-130">.NET Core SDK 3.0</span><span class="sxs-lookup"><span data-stu-id="24396-130">.NET Core SDK 3.0</span></span>](https://dotnet.microsoft.com/download)

::: moniker-end

* <span data-ttu-id="24396-131">Docker-Client 18.03 oder höher</span><span class="sxs-lookup"><span data-stu-id="24396-131">Docker client 18.03 or later</span></span>

  * <span data-ttu-id="24396-132">Linux-Verteilungen</span><span class="sxs-lookup"><span data-stu-id="24396-132">Linux distributions</span></span>
    * [<span data-ttu-id="24396-133">CentOS</span><span class="sxs-lookup"><span data-stu-id="24396-133">CentOS</span></span>](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [<span data-ttu-id="24396-134">Debian</span><span class="sxs-lookup"><span data-stu-id="24396-134">Debian</span></span>](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [<span data-ttu-id="24396-135">Fedora</span><span class="sxs-lookup"><span data-stu-id="24396-135">Fedora</span></span>](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [<span data-ttu-id="24396-136">Ubuntu</span><span class="sxs-lookup"><span data-stu-id="24396-136">Ubuntu</span></span>](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [<span data-ttu-id="24396-137">macOS</span><span class="sxs-lookup"><span data-stu-id="24396-137">macOS</span></span>](https://docs.docker.com/docker-for-mac/install/)
  * [<span data-ttu-id="24396-138">Windows</span><span class="sxs-lookup"><span data-stu-id="24396-138">Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)

* [<span data-ttu-id="24396-139">Git</span><span class="sxs-lookup"><span data-stu-id="24396-139">Git</span></span>](https://git-scm.com/download)

## <a name="download-the-sample-app"></a><span data-ttu-id="24396-140">Herunterladen der Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="24396-140">Download the sample app</span></span>

* <span data-ttu-id="24396-141">Laden Sie das Beispiel durch Klonen des [.NET Core-Docker-Repositorys](https://github.com/dotnet/dotnet-docker) herunter:</span><span class="sxs-lookup"><span data-stu-id="24396-141">Download the sample by cloning the [.NET Core Docker repository](https://github.com/dotnet/dotnet-docker):</span></span> 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a><span data-ttu-id="24396-142">Lokales Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="24396-142">Run the app locally</span></span>

* <span data-ttu-id="24396-143">Navigieren Sie zum Projektordner unter *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="24396-143">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="24396-144">Führen Sie den folgenden Befehl aus, um die App zu erstellen und lokal auszuführen:</span><span class="sxs-lookup"><span data-stu-id="24396-144">Run the following command to build and run the app locally:</span></span>

  ```dotnetcli
  dotnet run
  ```

* <span data-ttu-id="24396-145">Navigieren Sie in einem Browser zu `http://localhost:5000`, um die App zu testen.</span><span class="sxs-lookup"><span data-stu-id="24396-145">Go to `http://localhost:5000` in a browser to test the app.</span></span>

* <span data-ttu-id="24396-146">Drücken Sie Strg+C an der Eingabeaufforderung, um die App zu stoppen.</span><span class="sxs-lookup"><span data-stu-id="24396-146">Press Ctrl+C at the command prompt to stop the app.</span></span>

## <a name="run-in-a-linux-container"></a><span data-ttu-id="24396-147">Ausführen in einem Linux-Container</span><span class="sxs-lookup"><span data-stu-id="24396-147">Run in a Linux container</span></span>

* <span data-ttu-id="24396-148">Wechseln Sie im Docker-Client zu Linux-Containern.</span><span class="sxs-lookup"><span data-stu-id="24396-148">In the Docker client, switch to Linux containers.</span></span>

* <span data-ttu-id="24396-149">Navigieren Sie zum Dockerfile-Ordner unter *dotnet-docker/samples/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="24396-149">Navigate to the Dockerfile folder at *dotnet-docker/samples/aspnetapp*.</span></span>

* <span data-ttu-id="24396-150">Führen Sie die folgenden Befehle zum Erstellen und Ausführen des Beispiels in Docker aus:</span><span class="sxs-lookup"><span data-stu-id="24396-150">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  <span data-ttu-id="24396-151">Argumente des `build`-Befehls:</span><span class="sxs-lookup"><span data-stu-id="24396-151">The `build` command arguments:</span></span>
  * <span data-ttu-id="24396-152">Nennen Sie das Image „aspnetapp“.</span><span class="sxs-lookup"><span data-stu-id="24396-152">Name the image aspnetapp.</span></span>
  * <span data-ttu-id="24396-153">Suchen Sie nach der Dockerfil-Datei im aktuellen Ordner (Punkt am Ende).</span><span class="sxs-lookup"><span data-stu-id="24396-153">Look for the Dockerfile in the current folder (the period at the end).</span></span>

  <span data-ttu-id="24396-154">Argumente des run-Befehls:</span><span class="sxs-lookup"><span data-stu-id="24396-154">The run command arguments:</span></span>
  * <span data-ttu-id="24396-155">Ordnen Sie ein pseudo-TTY zu, und halten Sie es offen, wenn auch nicht angefügt.</span><span class="sxs-lookup"><span data-stu-id="24396-155">Allocate a pseudo-TTY and keep it open even if not attached.</span></span> <span data-ttu-id="24396-156">(Dieselben Auswirkungen wie `--interactive --tty`.)</span><span class="sxs-lookup"><span data-stu-id="24396-156">(Same effect as `--interactive --tty`.)</span></span>
  * <span data-ttu-id="24396-157">Entfernen Sie den Container, wenn er beendet wird.</span><span class="sxs-lookup"><span data-stu-id="24396-157">Automatically remove the container when it exits.</span></span>
  * <span data-ttu-id="24396-158">Ordnen Sie Port 5000 auf dem lokalen Computer Port 80 im Container zu.</span><span class="sxs-lookup"><span data-stu-id="24396-158">Map port 5000 on the local machine to port 80 in the container.</span></span>
  * <span data-ttu-id="24396-159">Nennen Sie den Container „aspnetcore_sample“.</span><span class="sxs-lookup"><span data-stu-id="24396-159">Name the container aspnetcore_sample.</span></span>
  * <span data-ttu-id="24396-160">Geben Sie das aspnetapp-Image an.</span><span class="sxs-lookup"><span data-stu-id="24396-160">Specify the aspnetapp image.</span></span>

* <span data-ttu-id="24396-161">Navigieren Sie in einem Browser zu `http://localhost:5000`, um die App zu testen.</span><span class="sxs-lookup"><span data-stu-id="24396-161">Go to `http://localhost:5000` in a browser to test the app.</span></span>

## <a name="run-in-a-windows-container"></a><span data-ttu-id="24396-162">Ausführen in einem Windows-Container</span><span class="sxs-lookup"><span data-stu-id="24396-162">Run in a Windows container</span></span>

* <span data-ttu-id="24396-163">Wechseln Sie im Docker-Windows zu Linux-Containern.</span><span class="sxs-lookup"><span data-stu-id="24396-163">In the Docker client, switch to Windows containers.</span></span>

<span data-ttu-id="24396-164">Navigieren Sie zum Dockerfile-Ordner unter `dotnet-docker/samples/aspnetapp`.</span><span class="sxs-lookup"><span data-stu-id="24396-164">Navigate to the docker file folder at `dotnet-docker/samples/aspnetapp`.</span></span>

* <span data-ttu-id="24396-165">Führen Sie die folgenden Befehle zum Erstellen und Ausführen des Beispiels in Docker aus:</span><span class="sxs-lookup"><span data-stu-id="24396-165">Run the following commands to build and run the sample in Docker:</span></span>

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* <span data-ttu-id="24396-166">Für Windows-Container benötigen Sie die IP-Adresse des Containers (Navigieren zu `http://localhost:5000` funktioniert nicht):</span><span class="sxs-lookup"><span data-stu-id="24396-166">For Windows containers, you need the IP address of the container (browsing to `http://localhost:5000` won't work):</span></span>
  * <span data-ttu-id="24396-167">Öffnen Sie eine weitere Eingabeaufforderung.</span><span class="sxs-lookup"><span data-stu-id="24396-167">Open up another command prompt.</span></span>
  * <span data-ttu-id="24396-168">Führen Sie `docker ps` aus, um die ausgeführten Container anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="24396-168">Run `docker ps` to see the running containers.</span></span> <span data-ttu-id="24396-169">Vergewissern Sie sich, dass der Container „aspnetcore_sample“ vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="24396-169">Verify that the "aspnetcore_sample" container is there.</span></span>
  * <span data-ttu-id="24396-170">Führen Sie `docker exec aspnetcore_sample ipconfig` aus, um die IP-Adresse des Containers anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="24396-170">Run `docker exec aspnetcore_sample ipconfig` to display the IP address of the container.</span></span> <span data-ttu-id="24396-171">Die Ausgabe des Befehls sieht wie im folgenden Beispiel aus:</span><span class="sxs-lookup"><span data-stu-id="24396-171">The output from the command looks like this example:</span></span>

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* <span data-ttu-id="24396-172">Kopieren Sie die IPv4-Adresse des Containers (z.B. 172.29.245.43), und fügen Sie sie in die Adressleiste des Browsers ein, um die App zu testen.</span><span class="sxs-lookup"><span data-stu-id="24396-172">Copy the container IPv4 address (for example, 172.29.245.43) and paste into the browser address bar to test the app.</span></span>

## <a name="build-and-deploy-manually"></a><span data-ttu-id="24396-173">Manuelles Erstellen und Bereitstellen</span><span class="sxs-lookup"><span data-stu-id="24396-173">Build and deploy manually</span></span>

<span data-ttu-id="24396-174">In einigen Szenarien möchten Sie eine App möglicherweise in einem Container bereitstellen, indem Sie die zur Laufzeit benötigten Anwendungsdateien in diesen kopieren.</span><span class="sxs-lookup"><span data-stu-id="24396-174">In some scenarios, you might want to deploy an app to a container by copying to it the application files that are needed at run time.</span></span> <span data-ttu-id="24396-175">In diesem Abschnitt wird gezeigt, wie Sie die Bereitstellung manuell ausführen.</span><span class="sxs-lookup"><span data-stu-id="24396-175">This section shows how to deploy manually.</span></span>

* <span data-ttu-id="24396-176">Navigieren Sie zum Projektordner unter *dotnet-docker/samples/aspnetapp/aspnetapp*.</span><span class="sxs-lookup"><span data-stu-id="24396-176">Navigate to the project folder at *dotnet-docker/samples/aspnetapp/aspnetapp*.</span></span>

* <span data-ttu-id="24396-177">Führen Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) aus:</span><span class="sxs-lookup"><span data-stu-id="24396-177">Run the [dotnet publish](/dotnet/core/tools/dotnet-publish) command:</span></span>

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  <span data-ttu-id="24396-178">Argumente des Befehls:</span><span class="sxs-lookup"><span data-stu-id="24396-178">The command arguments:</span></span>
  * <span data-ttu-id="24396-179">Erstellen Sie die Anwendung im Releasemodus (der Standardwert ist der Debugmodus).</span><span class="sxs-lookup"><span data-stu-id="24396-179">Build the application in release mode (the default is debug mode).</span></span>
  * <span data-ttu-id="24396-180">Erstellen Sie die Dateien im Ordner *published*.</span><span class="sxs-lookup"><span data-stu-id="24396-180">Create the files in the *published* folder.</span></span>

* <span data-ttu-id="24396-181">Führen Sie die Anwendung aus.</span><span class="sxs-lookup"><span data-stu-id="24396-181">Run the application.</span></span>

  * <span data-ttu-id="24396-182">Windows:</span><span class="sxs-lookup"><span data-stu-id="24396-182">Windows:</span></span>

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * <span data-ttu-id="24396-183">Linux:</span><span class="sxs-lookup"><span data-stu-id="24396-183">Linux:</span></span>

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* <span data-ttu-id="24396-184">Navigieren Sie zu `http://localhost:5000`, um die Startseite anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="24396-184">Browse to `http://localhost:5000` to see the home page.</span></span>

<span data-ttu-id="24396-185">Erstellen Sie eine neue Dockerfile-Datei, und verwenden Sie den `docker build .`-Befehl zum Erstellen des Containers, um die manuell veröffentlichte Anwendung innerhalb eines Docker-Containers zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="24396-185">To use the manually published application within a Docker container, create a new Dockerfile and use the `docker build .` command to build the container.</span></span>

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="24396-186">Die Dockerfile-Datei</span><span class="sxs-lookup"><span data-stu-id="24396-186">The Dockerfile</span></span>

<span data-ttu-id="24396-187">Dies ist die *Dockerfile*-Datei, die vom zuvor ausgeführten Befehl `docker build` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="24396-187">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="24396-188">Dabei wird `dotnet publish` auf die gleiche Weise wie in diesem Abschnitt zum Erstellen und Bereitstellen verwendet.</span><span class="sxs-lookup"><span data-stu-id="24396-188">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

::: moniker range=">= aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a><span data-ttu-id="24396-189">Die Dockerfile-Datei</span><span class="sxs-lookup"><span data-stu-id="24396-189">The Dockerfile</span></span>

<span data-ttu-id="24396-190">Dies ist die *Dockerfile*-Datei, die vom zuvor ausgeführten Befehl `docker build` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="24396-190">Here's the *Dockerfile* used by the `docker build` command you ran earlier.</span></span>  <span data-ttu-id="24396-191">Dabei wird `dotnet publish` auf die gleiche Weise wie in diesem Abschnitt zum Erstellen und Bereitstellen verwendet.</span><span class="sxs-lookup"><span data-stu-id="24396-191">It uses `dotnet publish` the same way you did in this section to build and deploy.</span></span>  

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

<span data-ttu-id="24396-192">Wie bereits in der vorherigen Dockerfile-Datei erwähnt, werden `*.csproj`-Dateien kopiert und als unterschiedliche *Ebenen* wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="24396-192">As noted in the preceding Dockerfile, the `*.csproj` files are copied and restored as distinct *layers*.</span></span> <span data-ttu-id="24396-193">Wenn der Befehl `docker build` ein Image erstellt, wird ein integrierter Cache verwendet.</span><span class="sxs-lookup"><span data-stu-id="24396-193">When the `docker build` command builds an image, it uses a built-in cache.</span></span> <span data-ttu-id="24396-194">Wenn sich die `*.csproj`-Dateien seit der letzten Ausführung des Befehls `docker build` nicht geändert haben, muss der `dotnet restore`-Befehl nicht noch mal ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="24396-194">If the `*.csproj` files haven't changed since the `docker build` command last ran, the `dotnet restore` command doesn't need to run again.</span></span> <span data-ttu-id="24396-195">Stattdessen wird der integrierte Cache für die entsprechende `dotnet restore`-Ebene verwendet.</span><span class="sxs-lookup"><span data-stu-id="24396-195">Instead, the built-in cache for the corresponding `dotnet restore` layer is reused.</span></span> <span data-ttu-id="24396-196">Weitere Informationen finden Sie unter [Best Practices für Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span><span class="sxs-lookup"><span data-stu-id="24396-196">For more information, see [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="24396-197">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="24396-197">Additional resources</span></span>

* [<span data-ttu-id="24396-198">docker build-Befehl</span><span class="sxs-lookup"><span data-stu-id="24396-198">Docker build command</span></span>](https://docs.docker.com/engine/reference/commandline/build)
* [<span data-ttu-id="24396-199">docker run-Befehl</span><span class="sxs-lookup"><span data-stu-id="24396-199">Docker run command</span></span>](https://docs.docker.com/engine/reference/commandline/run)
* <span data-ttu-id="24396-200">[Beispiel für ASP.NET Core-Docker](https://github.com/dotnet/dotnet-docker) (das in diesem Tutorial verwendete Beispiel)</span><span class="sxs-lookup"><span data-stu-id="24396-200">[ASP.NET Core Docker sample](https://github.com/dotnet/dotnet-docker) (The one used in this tutorial.)</span></span>
* [<span data-ttu-id="24396-201">Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="24396-201">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](../proxy-load-balancer.md)
* [<span data-ttu-id="24396-202">Arbeiten mit Visual Studio-Tools für Docker</span><span class="sxs-lookup"><span data-stu-id="24396-202">Working with Visual Studio Docker Tools</span></span>](./visual-studio-tools-for-docker.md)
* [<span data-ttu-id="24396-203">Debuggen mit Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="24396-203">Debugging with Visual Studio Code</span></span>](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [<span data-ttu-id="24396-204">GC mittels Docker und kleiner Containers</span><span class="sxs-lookup"><span data-stu-id="24396-204">GC using Docker and small containers</span></span>](xref:performance/memory#sc)

## <a name="next-steps"></a><span data-ttu-id="24396-205">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="24396-205">Next steps</span></span>

<span data-ttu-id="24396-206">Im Git-Repository, das die Beispiel-App enthält, finden Sie auch die Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="24396-206">The Git repository that contains the sample app also includes documentation.</span></span> <span data-ttu-id="24396-207">Eine Übersicht über die im Repository verfügbaren Ressourcen finden Sie in der [Infodatei](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span><span class="sxs-lookup"><span data-stu-id="24396-207">For an overview of the resources available in the repository, see [the README file](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md).</span></span> <span data-ttu-id="24396-208">Insbesondere erfahren Sie, wie Sie HTTPS implementieren:</span><span class="sxs-lookup"><span data-stu-id="24396-208">In particular, learn how to implement HTTPS:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="24396-209">Entwickeln von ASP.NET Core-Anwendungen mit Docker über HTTPS</span><span class="sxs-lookup"><span data-stu-id="24396-209">Developing ASP.NET Core Applications with Docker over HTTPS</span></span>](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)