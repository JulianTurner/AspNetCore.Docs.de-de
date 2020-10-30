---
title: Hosting ASP.net Core Abbilds in Containern mithilfe von Docker Compose mit HTTPS
author: ravipal
description: Erfahren Sie, wie Sie ASP.net Core Images mit docker Compose über HTTPS hosten.
monikerRange: '>= aspnetcore-2.1'
ms.author: ravipal
ms.custom: mvc
ms.date: 03/28/2020
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
uid: security/docker-compose-https
ms.openlocfilehash: 37a0142dac1e26afd26dbf2aad46bee20693652e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051641"
---
# <a name="hosting-aspnet-core-images-with-docker-compose-over-https"></a>Hosting ASP.net Core Images mit docker Compose über HTTPS


ASP.net Core verwendet [standardmäßig HTTPS](./enforcing-ssl.md). [Https](https://en.wikipedia.org/wiki/HTTPS) stützt sich auf [Zertifikate](https://en.wikipedia.org/wiki/Public_key_certificate) für Vertrauenswürdigkeit, Identität und Verschlüsselung.

In diesem Dokument wird erläutert, wie vorgefertigte Container Images mit HTTPS ausgeführt werden.

Weitere Informationen finden Sie [unter Entwickeln von ASP.net Core Anwendungen mit docker über HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) für Entwicklungsszenarien.

Für dieses Beispiel ist [docker 17,06](https://docs.docker.com/release-notes/docker-ce) oder höher des [docker-Clients](https://www.docker.com/products/docker)erforderlich.

## <a name="prerequisites"></a>Voraussetzungen

Für einige der Anweisungen in diesem Dokument ist das [.net Core 2,2 SDK](https://dotnet.microsoft.com/download) oder höher erforderlich.

## <a name="certificates"></a>Zertifikate

Ein Zertifikat von einer [Zertifizierungs](https://wikipedia.org/wiki/Certificate_authority) Stelle ist für das [Produktions Hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) für eine Domäne erforderlich. [Let's Encrypt](https://letsencrypt.org/) ist eine Zertifizierungsstelle, die kostenlose Zertifikate anbietet.

In diesem Dokument werden [selbst signierte Entwicklungs Zertifikate](https://wikipedia.org/wiki/Self-signed_certificate) für das Hosting von vordefinierten Images verwendet `localhost` . Die Anweisungen ähneln der Verwendung von Produktions Zertifikaten.

Für Produktions Zertifikate:

* Das `dotnet dev-certs` Tool ist nicht erforderlich.
* Zertifikate müssen nicht an dem Speicherort gespeichert werden, der in den Anweisungen verwendet wird. Speichern Sie die Zertifikate an einem beliebigen Speicherort außerhalb des Website Verzeichnisses.

Die Anweisungen im folgenden Abschnitt enthalten ein Volume zum Einbinden von Zertifikaten in Containern mithilfe der- `volumes` Eigenschaft in *docker-Compose. yml.* Sie können Zertifikate in Container Images mit einem `COPY` Befehl in einer *dockerfile-Datei* hinzufügen, dies wird jedoch nicht empfohlen. Das Kopieren von Zertifikaten in ein Abbild wird aus den folgenden Gründen nicht empfohlen:

* Es ist schwierig, dasselbe Image für Tests mit Entwickler Zertifikaten zu verwenden.
* Es ist schwierig, das gleiche Image für das Hosting mit Produktions Zertifikaten zu verwenden.
* Es besteht ein erhebliches Risiko für die Offenlegung von Zertifikaten.

## <a name="starting-a-container-with-https-support-using-docker-compose"></a>Starten eines Containers mit HTTPS-Unterstützung mithilfe von Docker Compose

Verwenden Sie die folgenden Anweisungen für die Betriebssystem Konfiguration.

### <a name="windows-using-linux-containers"></a>Windows mithilfe von Linux-Containern

Zertifikat generieren und lokalen Computer konfigurieren:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.

Erstellen Sie eine _docker-Compose. Debug. yml_ -Datei mit folgendem Inhalt:

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
Das in der Docker Compose-Datei angegebene Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.

Starten Sie den Container mit ASP.net Core, die für HTTPS konfiguriert sind:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="macos-or-linux"></a>macOS oder Linux

Zertifikat generieren und lokalen Computer konfigurieren:

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

`dotnet dev-certs https --trust` wird nur unter macOS und Windows unterstützt. Sie müssen Zertifikate unter Linux auf die gleiche Weise als vertrauenswürdig einstufen, die von Ihrer Distribution unterstützt wird. Es ist wahrscheinlich, dass Sie das Zertifikat in Ihrem Browser als vertrauenswürdig einstufen müssen.

Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.

Erstellen Sie eine _docker-Compose. Debug. yml_ -Datei mit folgendem Inhalt:

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx
    volumes:
      - ~/.aspnet/https:/https:ro
```
Das in der Docker Compose-Datei angegebene Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.

Starten Sie den Container mit ASP.net Core, die für HTTPS konfiguriert sind:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```

### <a name="windows-using-windows-containers"></a>Windows mithilfe von Windows-Containern

Zertifikat generieren und lokalen Computer konfigurieren:

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.

Erstellen Sie eine _docker-Compose. Debug. yml_ -Datei mit folgendem Inhalt:

```yaml
version: '3.4'

services:
  webapp:
    image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ports:
      - 80
      - 443
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ASPNETCORE_URLS=https://+:443;http://+:80
      - ASPNETCORE_Kestrel__Certificates__Default__Password=password
      - ASPNETCORE_Kestrel__Certificates__Default__Path=C:\https\aspnetapp.pfx
    volumes:
      - ${USERPROFILE}\.aspnet\https:C:\https:ro
```
Das in der Docker Compose-Datei angegebene Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.

Starten Sie den Container mit ASP.net Core, die für HTTPS konfiguriert sind:

```console
docker-compose -f "docker-compose.debug.yml" up -d
```
