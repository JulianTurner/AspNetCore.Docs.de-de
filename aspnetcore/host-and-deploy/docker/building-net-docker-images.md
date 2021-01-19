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
# <a name="docker-images-for-aspnet-core"></a>Docker-Images für ASP.NET Core

In diesem Tutorial wird gezeigt, wie eine ASP.NET Core-App in Docker-Containern ausgeführt wird.

In diesem Tutorial:
> [!div class="checklist"]
> * erhalten Sie Informationen über ASP.NET Core-Docker-Images
> * Herunterladen einer ASP.NET Core-Beispiel-App
> * Lokales Ausführen der Beispiel-App
> * Ausführen der Beispiel-App in Linux-Containern
> * Ausführen der Beispiel-App in Windows-Containern
> * Manuelles Erstellen und Bereitstellen

## <a name="aspnet-core-docker-images"></a>ASP.NET Core Docker-Images

Für dieses Tutorial laden Sie eine ASP.NET Core-Beispiel-App herunter, und führen sie in Docker-Containern aus. Das Beispiel funktioniert sowohl mit Linux- als auch mit Windows-Containern.

Die Dockerfile-Beispieldatei verwendet das [Docker-Feature für mehrstufige Builds](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) zur Erstellung und Ausführung in verschiedenen Containern. Die Erstellungs- und Ausführungscontainer werden aus Images erstellt, die im Docker-Hub von Microsoft bereitgestellt werden:

::: moniker range=">= aspnetcore-5.0"

* `dotnet/sdk`

  Das Beispiel verwendet dieses Image für die Erstellung der App. Das Image enthält das .NET SDK, das die Befehlszeilentools (CLI) enthält. Das Image ist für die lokale Entwicklung sowie für das Debuggen und für Komponententests optimiert. Aufgrund der installierten Tools für die Entwicklung und Kompilierung ist das Image relativ groß.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/sdk`

  Das Beispiel verwendet dieses Image für die Erstellung der App. Das Image enthält das .NET Core SDK mit den Befehlszeilentools (CLI). Das Image ist für die lokale Entwicklung sowie für das Debuggen und für Komponententests optimiert. Aufgrund der installierten Tools für die Entwicklung und Kompilierung ist das Image relativ groß.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `dotnet/aspnet`

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `dotnet/core/aspnet`

::: moniker-end

   Das Beispiel verwendet dieses Image zum Ausführen der App. Das Image enthält die ASP.NET Core-Runtime und -Bibliotheken und wurde für das Ausführen von Apps in der Produktion optimiert. Das Image wurde im Hinblick auf Bereitstellungs- und App-Startgeschwindigkeit entwickelt und ist daher relativ klein, sodass die Netzwerkleistung von Docker-Registrierung zu Docker-Host optimiert ist. Nur die zum Ausführen einer App benötigten Binärdateien und Inhalte werden in den Container kopiert. Die Inhalte sind bereit zur Ausführung und ermöglichen in kürzester Zeit nach dem `docker run` das Starten der App. Die dynamische Codekompilierung ist im Docker-Modell nicht erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

::: moniker range=">= aspnetcore-5.0"

* [.NET SDK 5.0](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* [.NET Core SDK 3.1](https://dotnet.microsoft.com/download)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core)

::: moniker-end

* Docker-Client 18.03 oder höher

  * Linux-Verteilungen
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    * [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    * [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
  * [macOS](https://docs.docker.com/docker-for-mac/install/)
  * [Windows](https://docs.docker.com/docker-for-windows/install/)

* [Git](https://git-scm.com/download)

## <a name="download-the-sample-app"></a>Herunterladen der Beispiel-App

* Laden Sie das Beispiel herunter, indem Sie das [.NET Docker-Repository](https://github.com/dotnet/dotnet-docker) klonen: 

  ```console
  git clone https://github.com/dotnet/dotnet-docker
  ```

## <a name="run-the-app-locally"></a>Lokales Ausführen der App

* Navigieren Sie zum Projektordner unter *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Führen Sie den folgenden Befehl aus, um die App zu erstellen und lokal auszuführen:

  ```dotnetcli
  dotnet run
  ```

* Navigieren Sie in einem Browser zu `http://localhost:5000`, um die App zu testen.

* Drücken Sie Strg+C an der Eingabeaufforderung, um die App zu stoppen.

## <a name="run-in-a-linux-container"></a>Ausführen in einem Linux-Container

* Wechseln Sie im Docker-Client [zu Linux-Containern](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* Navigieren Sie zum Dockerfile-Ordner unter *dotnet-docker/samples/aspnetapp*.

* Führen Sie die folgenden Befehle zum Erstellen und Ausführen des Beispiels in Docker aus:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm -p 5000:80 --name aspnetcore_sample aspnetapp
  ```

  Argumente des `build`-Befehls:
  * Nennen Sie das Image „aspnetapp“.
  * Suchen Sie nach der Dockerfil-Datei im aktuellen Ordner (Punkt am Ende).

  Argumente des run-Befehls:
  * Ordnen Sie ein pseudo-TTY zu, und halten Sie es offen, wenn auch nicht angefügt. (Dieselben Auswirkungen wie `--interactive --tty`.)
  * Entfernen Sie den Container, wenn er beendet wird.
  * Ordnen Sie Port 5000 auf dem lokalen Computer Port 80 im Container zu.
  * Nennen Sie den Container „aspnetcore_sample“.
  * Geben Sie das aspnetapp-Image an.

* Navigieren Sie in einem Browser zu `http://localhost:5000`, um die App zu testen.

## <a name="run-in-a-windows-container"></a>Ausführen in einem Windows-Container

* Wechseln Sie im Docker-Client [zu Windows-Containern](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

Navigieren Sie zum Dockerfile-Ordner unter `dotnet-docker/samples/aspnetapp`.

* Führen Sie die folgenden Befehle zum Erstellen und Ausführen des Beispiels in Docker aus:

  ```console
  docker build -t aspnetapp .
  docker run -it --rm --name aspnetcore_sample aspnetapp
  ```

* Für Windows-Container benötigen Sie die IP-Adresse des Containers (Navigieren zu `http://localhost:5000` funktioniert nicht):
  * Öffnen Sie eine weitere Eingabeaufforderung.
  * Führen Sie `docker ps` aus, um die ausgeführten Container anzuzeigen. Vergewissern Sie sich, dass der Container „aspnetcore_sample“ vorhanden ist.
  * Führen Sie `docker exec aspnetcore_sample ipconfig` aus, um die IP-Adresse des Containers anzuzeigen. Die Ausgabe des Befehls sieht wie im folgenden Beispiel aus:

    ```console
    Ethernet adapter Ethernet:

       Connection-specific DNS Suffix  . : contoso.com
       Link-local IPv6 Address . . . . . : fe80::1967:6598:124:cfa3%4
       IPv4 Address. . . . . . . . . . . : 172.29.245.43
       Subnet Mask . . . . . . . . . . . : 255.255.240.0
       Default Gateway . . . . . . . . . : 172.29.240.1
    ```

* Kopieren Sie die IPv4-Adresse des Containers (z.B. 172.29.245.43), und fügen Sie sie in die Adressleiste des Browsers ein, um die App zu testen.

## <a name="build-and-deploy-manually"></a>Manuelles Erstellen und Bereitstellen

In einigen Szenarios müssen Sie möglicherweise eine App in einem Container bereitstellen, indem Sie die erforderlichen Ressourcen zur Laufzeit kopieren. In diesem Abschnitt wird gezeigt, wie Sie die Bereitstellung manuell ausführen.

* Navigieren Sie zum Projektordner unter *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Führen Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) aus:

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Argumente des Befehls:
  * erstellen die App im Releasemodus (der Debugmodus ist der Standardmodus).
  * erstellen die Ressourcen im Ordner *published* (Veröffentlicht).

* Führen Sie die App aus.

  * Windows:

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux:

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* Navigieren Sie zu `http://localhost:5000`, um die Startseite anzuzeigen.

Erstellen Sie eine neue *Dockerfile*, und verwenden Sie den Befehl `docker build .` zum Erstellen des Containers, um die manuell veröffentlichte App in einem Docker-Container zu verwenden.

::: moniker range=">= aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:5.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Die Dockerfile-Datei

Dies ist die *Dockerfile*-Datei, die vom zuvor ausgeführten Befehl `docker build` verwendet wurde.  Dabei wird `dotnet publish` auf die gleiche Weise wie in diesem Abschnitt zum Erstellen und Bereitstellen verwendet.  

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

In der vorherigen *Dockerfile* werden die `*.csproj`-Dateien kopiert und in unterschiedlichen *Ebenen* wiederhergestellt. Wenn der Befehl `docker build` ein Image erstellt, wird ein integrierter Cache verwendet. Wenn sich die `*.csproj`-Dateien seit der letzten Ausführung des Befehls `docker build` nicht geändert haben, muss der `dotnet restore`-Befehl nicht noch mal ausgeführt werden. Stattdessen wird der integrierte Cache für die entsprechende `dotnet restore`-Ebene verwendet. Weitere Informationen finden Sie unter [Best Practices für Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:3.0 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Die Dockerfile-Datei

Dies ist die *Dockerfile*-Datei, die vom zuvor ausgeführten Befehl `docker build` verwendet wurde.  Dabei wird `dotnet publish` auf die gleiche Weise wie in diesem Abschnitt zum Erstellen und Bereitstellen verwendet.  

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

Wie bereits in der vorherigen Dockerfile-Datei erwähnt, werden `*.csproj`-Dateien kopiert und als unterschiedliche *Ebenen* wiederhergestellt. Wenn der Befehl `docker build` ein Image erstellt, wird ein integrierter Cache verwendet. Wenn sich die `*.csproj`-Dateien seit der letzten Ausführung des Befehls `docker build` nicht geändert haben, muss der `dotnet restore`-Befehl nicht noch mal ausgeführt werden. Stattdessen wird der integrierte Cache für die entsprechende `dotnet restore`-Ebene verwendet. Weitere Informationen finden Sie unter [Best Practices für Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#leverage-build-cache).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Die Dockerfile-Datei

Dies ist die *Dockerfile*-Datei, die vom zuvor ausgeführten Befehl `docker build` verwendet wurde. Dabei wird `dotnet publish` auf die gleiche Weise wie in diesem Abschnitt zum Erstellen und Bereitstellen verwendet.  

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [docker build-Befehl](https://docs.docker.com/engine/reference/commandline/build)
* [docker run-Befehl](https://docs.docker.com/engine/reference/commandline/run)
* [Beispiel für ASP.NET Core-Docker](https://github.com/dotnet/dotnet-docker) (das in diesem Tutorial verwendete Beispiel)
* [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](../proxy-load-balancer.md)
* [Arbeiten mit Visual Studio-Tools für Docker](./visual-studio-tools-for-docker.md)
* [Debuggen mit Visual Studio Code](https://code.visualstudio.com/docs/nodejs/debugging-recipes#_debug-nodejs-in-docker-containers)
* [GC mittels Docker und kleiner Containers](xref:performance/memory#sc)

## <a name="next-steps"></a>Nächste Schritte

Im Git-Repository, das die Beispiel-App enthält, finden Sie auch die Dokumentation. Eine Übersicht über die im Repository verfügbaren Ressourcen finden Sie in der [Infodatei](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/README.md). Insbesondere erfahren Sie, wie Sie HTTPS implementieren:

> [!div class="nextstepaction"]
> [Entwickeln von ASP.NET Core-Anwendungen mit Docker über HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md)
