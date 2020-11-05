---
title: Visual Studio-Containertools mit ASP.NET Core
author: spboyer
description: Informationen zum Verwenden der Visual Studio-Tools und Docker für Windows, um Ihre ASP.NET Core-App in Container zu packen.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
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
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: 2cfd200c44290a0931cdeb2f68e99b90c11aa612
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059818"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a>Visual Studio-Containertools mit ASP.NET Core

Visual Studio 2017 und höhere Versionen unterstützen das Erstellen, Debuggen und Ausführen von ASP.NET Core-Container-Apps, die für .NET Core entwickelt werden. Sowohl Windows- als auch Linux-Container werden unterstützt.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

* [Docker für Windows](https://docs.docker.com/docker-for-windows/install/)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload für die **plattformübergreifende .NET Core-Entwicklung**.

## <a name="installation-and-setup"></a>Installation und Einrichtung

Lesen Sie vor der Installation von Docker zunächst [Docker for Windows: What to know before you install (Docker Desktop für Windows: Was vor der Installation zu beachten ist)](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install). Installieren Sie anschließend [Docker für Windows](https://docs.docker.com/docker-for-windows/install/).

**[Freigegebene Laufwerke](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker für Windows müssen so konfiguriert sein, dass sie die Volumezuordnung und das Debuggen unterstützen. Klicken Sie mit der rechten Maustaste in der Taskleiste auf das Docker-Symbol, und rufen Sie anschließend **Einstellungen** > **Freigegebene Laufwerke** aus. Wählen Sie das Laufwerk aus, auf dem Docker Dateien speichert. Klicken Sie auf **Übernehmen**.

![Dialogfeld zur Auswahl des lokalen Laufwerks C, das für Container freigegeben werden soll](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> Visual Studio 2017 Version 15.6 und höher zeigt später eine Eingabeaufforderung an, wenn die **Freigegebenen Laufwerke** nicht konfiguriert sind.

## <a name="add-a-project-to-a-docker-container"></a>Hinzufügen eines Projekts zu einem Docker-Container

Zur Containerisierung eines ASP.NET Core-Projekts muss das Projekt .NET Core als Zielplattform verwenden. Sowohl Linux- als auch Windows-Container werden unterstützt.

Wenn Sie Docker-Unterstützung zu einem Projekt hinzufügen, können Sie zwischen einem Windows- oder einem Linux-Container auswählen. Der Docker-Host muss den gleichen Containertyp ausführen. Wenn Sie den Containertyp in der ausgeführten Docker-Instanz ändern möchten, klicken Sie mit der rechten Maustaste auf der Taskleiste auf das Docker-Symbol, und wählen Sie **Switch to Windows containers** (Zu Windows-Containern wechseln) oder **Switch to Linux container** (Zu Linux-Containern wechseln) aus.

### <a name="new-app"></a>Neue App

Wenn Sie mithilfe der Projektvorlage **ASP.NET Core-Webanwendung** eine neue App erstellen, aktivieren Sie das Kontrollkästchen **Enable Docker Support** (Docker-Unterstützung aktivieren):

![Kontrollkästchen „Enable Docker Support“ (Docker-Unterstützung aktivieren)](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

Wenn .NET Core das Zielframework ist, können Sie in der Dropdownliste **OS** (Betriebssystem) einen Containertyp auswählen.

### <a name="existing-app"></a>Vorhandene App

Es gibt für ASP.NET Core-Projekte, die auf .NET Core abzielen, zwei Möglichkeiten, Docker-Unterstützung über Tools hinzuzufügen. Öffnen Sie das Projekt in Visual Studio, und wählen Sie eine der folgenden Optionen aus:

* Wählen Sie aus dem **Projektmenü** **Docker-Unterstützung** aus.
* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und rufen Sie anschließend **Hinzufügen** > **Docker-Unterstützung** auf.

Mit den Containertools von Visual Studio können Sie Docker nicht zu einem vorhandenen ASP.NET Core-Projekt hinzufügen, das auf .NET Framework abzielt.

## <a name="dockerfile-overview"></a>Übersicht über die Dockerfile-Datei

Eine *Dockerfile* -Datei, der wichtigste Bestandteil beim Erstellen eines endgültigen Docker-Images, wird dem Projektstamm hinzugefügt. Verweisen Sie auf einen [Dockerfile-Verweis](https://docs.docker.com/engine/reference/builder/), damit Sie einen Überblick über die darin enthaltenen Befehle erlangen. Diese spezielle *Dockerfile* -Datei verwendet einen [mehrstufigen Build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/), der vier unterschiedlich benannte Buildschritte enthält:

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

Die obige *Dockerfile* -Datei basiert auf dem [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/)-Image. Dieses Basisimage enthält die ASP.NET Core-Runtime und NuGet-Pakete. Zur Verbesserung der Startleistung wurden die Pakete mit einem JIT-Compiler (Just-In-Time) übersetzt.

Wenn im neuen Projektdialogfeld das Kontrollkästchen **Configure for HTTPS** (Für HTTPS konfigurieren) aktiviert ist, werden durch die *Dockerfile* -Datei zwei Ports verfügbar gemacht. Ein Port wird für den HTTP-Datenverkehr, der andere für HTTPS verwendet. Wenn dieses Kontrollkästchen nicht aktiviert ist, wird nur der Port 80 für den HTTP-Datenverkehr verfügbar gemacht.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

Die obige *Dockerfile* -Datei basiert auf dem [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/)-Image. Dieses Basisimage enthält die ASP.NET Core-NuGet-Pakete, die mit einem JIT-Compiler übersetzt wurden und dadurch die Startleistung verbessern.

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a>Hinzufügen eines Containerorchestrators zu einer App

In Version 15.7 oder in früheren Versionen von Visual Studio 2017 wird als Containerorchestrierungslösung ausschließlich [Docker Compose](https://docs.docker.com/compose/overview/) unterstützt. Die Docker Compose-Artefakte werden über **Hinzufügen** > **Docker-Unterstützung** hinzugefügt.

Ab Version 15.8 von Visual Studio 2017 wird eine Orchestrierungslösung erst bei Aufforderung hinzugefügt. Klicken Sie mit der rechten Maustaste im **Projektmappen-Explorer** auf das Projekt, und rufen Sie **Hinzufügen** > **Container Orchestrator Support** (Unterstützung für Containerorchestrator) auf. Die folgenden Optionen sind verfügbar: 

* [Docker Compose](#docker-compose)
* [Service Fabric](#service-fabric)
* [Kubernetes/Helm ](https://helm.sh/)

### <a name="docker-compose"></a>Docker Compose

Die Containertools von Visual Studio fügen der Projektmappe ein *docker-compose* -Projekt mit den folgenden Dateien hinzu:

* *docker-compose.dcproj:* Hierbei handelt es sich um die Datei, die das Projekt darstellt. Diese enthält ein `<DockerTargetOS>`-Element, mit dem das zu verwendende Betriebssystem angegeben wird.
* *.dockerignore:* Diese Datei enthält eine Liste mit Mustern für Dateien und Verzeichnisse, die beim Generieren eines Buildkontexts ausgeschlossen werden sollen.
* *docker-compose.yml:* Hierbei handelt es sich um die [Docker Compose](https://docs.docker.com/compose/overview/)-Basisdatei, die zum Definieren der Sammlung der Images verwendet wird, die jeweils mit `docker-compose build` und `docker-compose run` erstellt und ausgeführt werden.
* *docker-compose.override.yml:* Dies ist eine optionale Datei, die von Docker Compose gelesen wird und Einstellungen zur Überschreibung von Konfigurationen für Dienste enthält. Visual Studio führt `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` aus, um die Dateien zusammenzuführen.

Die Datei *docker-compose.yml* verweist auf den Namen des Images, das beim Ausführen des Projekts erstellt wird:

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

In dem zuvor genannten Beispiel generiert `image: hellodockertools` das Image `hellodockertools:dev`, wenn die App im Modus **Debuggen** ausgeführt wird. Das `hellodockertools:latest`-Image wird generiert, wenn die App im Modus **Release** ausgeführt wird.

Stellen Sie dem Imagenamen den Benutzernamen für [Docker Hub](https://hub.docker.com/) voran (z.B. `dockerhubusername/hellodockertools`), wenn das Image per Push in die Registrierung übertragen werden soll. Stattdessen können Sie auch den Imagenamen ändern, sodass er die private Registrierungs-URL, die von der Konfiguration abhängig ist (z.B. `privateregistry.domain.com/hellodockertools`) enthält.

Wenn Sie ein anderes Verhalten basierend auf der Buildkonfiguration wünschen (z.B. Debug oder Release), fügen Sie konfigurationsspezifische *docker-compose* Dateien hinzu. Die Dateien sollten entsprechend der Buildkonfiguration benannt werden (z.B. *docker-compose.vs.debug.yml* und *docker-compose.vs.release.yml* ) und am gleichen Speicherort wie die Datei *docker-compose-override.yml* gespeichert werden. 

Mithilfe der konfigurationsspezifischen Außerkraftsetzungsdateien können Sie verschiedene Konfigurationseinstellungen (z.B. Umgebungsvariablen oder Einstiegspunkte) für Debug- und Releasebuildkonfigurationen angeben.

Damit Docker Compose eine Option für die Ausführung in Visual Studio anzeigen kann, muss das Docker-Projekt als Startprojekt festgelegt sein.

### <a name="service-fabric"></a>Service Fabric

Zusätzlich zu den grundlegenden [Voraussetzungen](#prerequisites) ist für [Service Fabric](/azure/service-fabric/) als Orchestrierungslösung Folgendes erforderlich:

* Version 2.6 oder eine höher des [Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
* die Visual Studio-Workload **Azure-Entwicklung**

Die Ausführung von Linux-Containern auf dem lokalen Entwicklungscluster unter Windows wird von Service Fabric nicht unterstützt. Wenn für das Projekt bereits ein Linux-Container verwendet wird, fordert Visual Studio Sie dazu auf, stattdessen einen Windows-Container zu nutzen.

Die Containertools von Visual Studio führen die folgenden Aufgaben aus:

* Ein **Service Fabric-Anwendungsprojekt** mit dem Namen *&lt;Projektname&gt;Application* wird der Projektmappe hinzugefügt.
* Eine *Dockerfile* -Datei und eine *DOCKERIGNORE* -Datei werden dem ASP.NET Core-Projekt hinzugefügt. Wenn im ASP.NET Core-Projekt bereits eine *Dockerfile* -Datei vorhanden ist, wird diese in *Dockerfile.original* umbenannt. Erstellt wird eine neue *Dockerfile* -Datei, die der folgenden ähnelt:

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* Ein `<IsServiceFabricServiceProject>`-Element wird der *CSPROJ* -Datei des ASP.NET Core-Projekts hinzugefügt:

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* Ein *PackageRoot* -Ordner wird dem ASP.NET Core-Projekt hinzugefügt. Der Ordner enthält das Dienstmanifest und die Einstellungen für den neuen Dienst.

Weitere Informationen finden Sie unter [Bereitstellen einer .NET-App in einem Windows-Container in Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).

## <a name="debug"></a>Debug

Wählen Sie in der Symbolleiste im Dropdownmenü „Debuggen“ die Option **Docker** aus, und starten Sie das Debuggen der Anwendung. In der Ansicht **Docker** im Fenster **Ausgabe** werden die folgenden Aktionen angezeigt:

::: moniker range=">= aspnetcore-2.1"

* Das Tag *2.1-aspnetcore-runtime* des *microsoft/dotnet* -Runtimeimages wird abgerufen (falls das Image noch nicht im Cache vorhanden ist). Das Image installiert die Runtimes für ASP.NET Core und .NET Core sowie die zugehörigen Bibliotheken. Es ist für die Ausführung von ASP.NET Core-Apps in einer Produktionsumgebung optimiert.
* Die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` wird innerhalb des Containers auf `Development` festgelegt.
* Zwei dynamisch zugewiesene Ports werden verfügbar gemacht, wobei einer für HTTP und der andere für HTTPS verwendet wird. Der Port, der „localhost“ zugewiesen ist, kann mit dem Befehl `docker ps` abgefragt werden.
* Die App wird in den Container kopiert.
* Der Standardbrowser wird mit dem dynamisch zugewiesenen Port gestartet, wobei der Debugger an den Container angehängt wird.

Das resultierende Docker-Image der App wird mit dem Tag *dev* versehen. Das Image basiert auf dem Tag *2.1-aspnetcore-runtime* des Basisimages *microsoft/dotnet*. Führen Sie im Fenster **Paket-Manager-Konsole** den Befehl `docker images` aus. Die Images auf dem Computer werden angezeigt:

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* Das Runtime-Image *microsoft/aspnetcore* wird geladen (falls es sich nicht bereits im Cache befindet).
* Die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` wird innerhalb des Containers auf `Development` festgelegt.
* Port 80 wird verfügbar gemacht und einem dynamisch zugewiesenen Port für Localhost zugewiesen. Der Port wird vom Docker-Host bestimmt und kann mit dem Befehl `docker ps` abgefragt werden.
* Die App wird in den Container kopiert.
* Der Standardbrowser wird mit dem dynamisch zugewiesenen Port gestartet, wobei der Debugger an den Container angehängt wird.

Das resultierende Docker-Image der App wird mit dem Tag *dev* versehen. Das Image basiert auf dem Basisimage *microsoft/aspnetcore*. Führen Sie im Fenster **Paket-Manager-Konsole** den Befehl `docker images` aus. Die Images auf dem Computer werden angezeigt:

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> Das *dev* -Image enthält keine App-Inhalte, da **Debug** -Konfigurationen durch das Einbinden von Volumes für die iterative Funktionalität sorgen. Verwenden Sie zum Verschieben eines Images die Konfiguration **Release**.

Führen Sie in der Paket-Manager-Konsole den Befehl `docker ps` aus. Beachten Sie, dass die App mithilfe des Containers ausgeführt wird:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a>Bearbeiten und Fortfahren

Änderungen an statischen Dateien und Razor-Ansichten werden automatisch aktualisiert, wobei kein Kompilierungsschritt erforderlich ist. Nehmen Sie die Änderung vor, speichern Sie die Datei, und aktualisieren Sie die Browseransicht, um die Aktualisierung anzuzeigen.

Änderungen an Codedateien erfordern eine Kompilierung und einen Neustart von Kestrel im Container. Nachdem Sie die Änderung vorgenommen haben, drücken Sie `CTRL+F5`, um den Prozess durchzuführen und die App im Container zu starten. Der Docker-Container wird nicht erneut erstellt oder angehalten. Führen Sie in der Paket-Manager-Konsole den Befehl `docker ps` aus. Beachten Sie, dass der ursprüngliche Container immer noch genau so ausgeführt wird wie vor zehn Minuten:

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a>Veröffentlichen von Docker-Images

Nachdem der Entwicklungs- und Debugzyklus für die App abgeschlossen wurde, kann mit den Containertools von Visual Studio das Produktionsimage der App erstellt werden. Wählen Sie im Dropdownmenü „Konfiguration“ die Option **Release** aus, und erstellen Sie die App. Die Tools rufen das Kompilierungs-/Veröffentlichungsimage von Docker Hub ab (falls es nicht bereits im Cache vorhanden ist). Ein Image mit dem Tag *latest* wird erstellt und kann mittels Push an die private Registrierung oder an den Docker-Hub übertragen werden.

Mit dem Befehl `docker images` können Sie die Liste der Images in der Paket-Manager-Konsole anzeigen. Dadurch werden Informationen angezeigt, die mit denen der folgenden Ausgabe vergleichbar sind:

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

Die `microsoft/aspnetcore-build`- und `microsoft/aspnetcore`-Images, die in der obigen Ausgabe zu sehen sind, werden ab .NET Core 2.1 durch `microsoft/dotnet`-Images ersetzt. Weitere Informationen finden Sie in der [Ankündigung zur Migration der Docker-Repositorys](https://github.com/aspnet/Announcements/issues/298).

::: moniker-end

> [!NOTE]
> Der Befehl `docker images` gibt Vermittlerimages mit Repositorynamen und -tags zurück, die als *\<none>* gekennzeichnet sind (oben nicht aufgeführt). Diese unbenannten Images werden von der [mehrstufig erstellten](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile* -Datei erstellt. Sie verbessern die Effizienz der Erstellung des endgültigen Images. D.h., nur die notwendigen Ebenen werden erneut erstellt, wenn Änderungen auftreten. Wenn die Vermittlerimages nicht mehr benötigt werden, löschen Sie sie über den Befehl [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

Möglicherweise wird angenommen, dass das Produktions- oder Releaseimage im Vergleich zum *dev* -Image kleiner ist. Aufgrund der Volumezuordnung wurden der Debugger und die App über den lokalen Computer und nicht innerhalb des Containers ausgeführt. Im *neusten* Image wurde der benötigte App-Code gepackt, um die App auf einem Hostcomputer auszuführen. Aus diesem Grund ist das Delta die Größe des App-Codes.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Containerentwicklung mit Visual Studio](/visualstudio/containers)
* [Azure Service Fabric: Vorbereiten der Entwicklungsumgebung](/azure/service-fabric/service-fabric-get-started)
* [Bereitstellen einer .NET-App in einem Windows-Container in Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [Problembehandlung bei der Visual Studio-Entwicklung mit Docker](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [GitHub-Repository mit Visual Studio-Containertools](https://github.com/Microsoft/DockerTools)
* [GC mittels Docker und kleiner Containers](xref:performance/memory#sc)
