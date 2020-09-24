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
# <a name="docker-images-for-aspnet-core"></a>Docker-Images für ASP.NET Core

In diesem Tutorial wird gezeigt, wie eine ASP.NET Core-App in Docker-Containern ausgeführt wird.

In diesem Tutorial:
> [!div class="checklist"]
> * Allgemeines zu Docker-Images in Microsoft .NET Core
> * Herunterladen einer ASP.NET Core-Beispiel-App
> * Lokales Ausführen der Beispiel-App
> * Ausführen der Beispiel-App in Linux-Containern
> * Ausführen der Beispiel-App in Windows-Containern
> * Manuelles Erstellen und Bereitstellen

## <a name="aspnet-core-docker-images"></a>ASP.NET Core Docker-Images

Für dieses Tutorial laden Sie eine ASP.NET Core-Beispiel-App herunter, und führen sie in Docker-Containern aus. Das Beispiel funktioniert sowohl mit Linux- als auch mit Windows-Containern.

Die Dockerfile-Beispieldatei verwendet das [Docker-Feature für mehrstufige Builds](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) zur Erstellung und Ausführung in verschiedenen Containern. Die Erstellungs- und Ausführungscontainer werden aus Images erstellt, die im Docker-Hub von Microsoft bereitgestellt werden:

* `dotnet/core/sdk`

  Das Beispiel verwendet dieses Image für die Erstellung der App. Das Image enthält das .NET Core SDK mit den Befehlszeilentools (CLI). Das Image ist für die lokale Entwicklung sowie für das Debuggen und für Komponententests optimiert. Die für Entwicklung und Kompilierung installierten Tools machen dies zu einem relativ großen Image. 

* `dotnet/core/aspnet`

   Das Beispiel verwendet dieses Image zum Ausführen der App. Das Image enthält die ASP.NET Core-Runtime und -Bibliotheken und wurde für das Ausführen von Apps in der Produktion optimiert. Das Image wurde im Hinblick auf Bereitstellungs- und App-Startgeschwindigkeit entwickelt und ist daher relativ klein, sodass die Netzwerkleistung von Docker-Registrierung zu Docker-Host optimiert ist. Nur die zum Ausführen einer App benötigten Binärdateien und Inhalte werden in den Container kopiert. Die Inhalte sind bereit zur Ausführung und ermöglichen in kürzester Zeit nach dem `Docker run` das Starten der App. Die dynamische Codekompilierung ist im Docker-Modell nicht erforderlich.

## <a name="prerequisites"></a>Voraussetzungen
::: moniker range="< aspnetcore-3.0"

* [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download/dotnet-core)
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* [.NET Core SDK 3.0](https://dotnet.microsoft.com/download)

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

* Laden Sie das Beispiel durch Klonen des [.NET Core-Docker-Repositorys](https://github.com/dotnet/dotnet-docker) herunter: 

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

* Wechseln Sie im Docker-Client zu Linux-Containern.

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

* Wechseln Sie im Docker-Windows zu Linux-Containern.

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

In einigen Szenarien möchten Sie eine App möglicherweise in einem Container bereitstellen, indem Sie die zur Laufzeit benötigten Anwendungsdateien in diesen kopieren. In diesem Abschnitt wird gezeigt, wie Sie die Bereitstellung manuell ausführen.

* Navigieren Sie zum Projektordner unter *dotnet-docker/samples/aspnetapp/aspnetapp*.

* Führen Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) aus:

  ```dotnetcli
  dotnet publish -c Release -o published
  ```

  Argumente des Befehls:
  * Erstellen Sie die Anwendung im Releasemodus (der Standardwert ist der Debugmodus).
  * Erstellen Sie die Dateien im Ordner *published*.

* Führen Sie die Anwendung aus.

  * Windows:

    ```dotnetcli
    dotnet published\aspnetapp.dll
    ```

  * Linux:

    ```dotnetcli
    dotnet published/aspnetapp.dll
    ```

* Navigieren Sie zu `http://localhost:5000`, um die Startseite anzuzeigen.

Erstellen Sie eine neue Dockerfile-Datei, und verwenden Sie den `docker build .`-Befehl zum Erstellen des Containers, um die manuell veröffentlichte Anwendung innerhalb eines Docker-Containers zu verwenden.

::: moniker range="< aspnetcore-3.0"

```dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS runtime
WORKDIR /app
COPY published/aspnetapp.dll ./
ENTRYPOINT ["dotnet", "aspnetapp.dll"]
```

### <a name="the-dockerfile"></a>Die Dockerfile-Datei

Dies ist die *Dockerfile*-Datei, die vom zuvor ausgeführten Befehl `docker build` verwendet wurde.  Dabei wird `dotnet publish` auf die gleiche Weise wie in diesem Abschnitt zum Erstellen und Bereitstellen verwendet.  

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