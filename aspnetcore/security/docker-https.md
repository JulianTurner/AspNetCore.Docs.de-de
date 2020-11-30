---
title: Hosting von ASP.net Core Images mit docker über HTTPS
author: rick-anderson
description: Erfahren Sie, wie Sie ASP.net Core Images mit docker über HTTPS hosten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
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
uid: security/docker-https
ms.openlocfilehash: a4aac2ce06fee20bdef157efc361f3099a217b1a
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96332153"
---
# <a name="hosting-aspnet-core-images-with-docker-over-https"></a><span data-ttu-id="9740a-103">Hosting von ASP.net Core Images mit docker über HTTPS</span><span class="sxs-lookup"><span data-stu-id="9740a-103">Hosting ASP.NET Core images with Docker over HTTPS</span></span>

<span data-ttu-id="9740a-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9740a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9740a-105">ASP.net Core verwendet [standardmäßig HTTPS](./enforcing-ssl.md).</span><span class="sxs-lookup"><span data-stu-id="9740a-105">ASP.NET Core uses [HTTPS by default](./enforcing-ssl.md).</span></span> <span data-ttu-id="9740a-106">[Https](https://en.wikipedia.org/wiki/HTTPS) stützt sich auf [Zertifikate](https://en.wikipedia.org/wiki/Public_key_certificate) für Vertrauenswürdigkeit, Identität und Verschlüsselung.</span><span class="sxs-lookup"><span data-stu-id="9740a-106">[HTTPS](https://en.wikipedia.org/wiki/HTTPS) relies on [certificates](https://en.wikipedia.org/wiki/Public_key_certificate) for trust, identity, and encryption.</span></span>

<span data-ttu-id="9740a-107">In diesem Dokument wird erläutert, wie vorgefertigte Container Images mit HTTPS ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="9740a-107">This document explains how to run pre-built container images with HTTPS.</span></span>

<span data-ttu-id="9740a-108">Weitere Informationen finden Sie [unter Entwickeln von ASP.net Core Anwendungen mit docker über HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) für Entwicklungsszenarien.</span><span class="sxs-lookup"><span data-stu-id="9740a-108">See [Developing ASP.NET Core Applications with Docker over HTTPS](https://github.com/dotnet/dotnet-docker/blob/master/samples/run-aspnetcore-https-development.md) for development scenarios.</span></span>

<span data-ttu-id="9740a-109">Für dieses Beispiel ist [docker 17,06](https://docs.docker.com/release-notes/docker-ce) oder höher des [docker-Clients](https://www.docker.com/products/docker)erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9740a-109">This sample requires [Docker 17.06](https://docs.docker.com/release-notes/docker-ce) or later of the [Docker client](https://www.docker.com/products/docker).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9740a-110">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="9740a-110">Prerequisites</span></span>

<span data-ttu-id="9740a-111">Für einige der Anweisungen in diesem Dokument ist das [.net Core 2,2 SDK](https://dotnet.microsoft.com/download) oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9740a-111">The [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download) or later is required for some of the instructions in this document.</span></span>

## <a name="certificates"></a><span data-ttu-id="9740a-112">Zertifikate</span><span class="sxs-lookup"><span data-stu-id="9740a-112">Certificates</span></span>

<span data-ttu-id="9740a-113">Ein Zertifikat von einer [Zertifizierungs](https://wikipedia.org/wiki/Certificate_authority) Stelle ist für das [Produktions Hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) für eine Domäne erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9740a-113">A certificate from a [certificate authority](https://wikipedia.org/wiki/Certificate_authority) is required for [production hosting](https://blogs.msdn.microsoft.com/webdev/2017/11/29/configuring-https-in-asp-net-core-across-different-platforms/) for a domain.</span></span> <span data-ttu-id="9740a-114">[Let's Encrypt](https://letsencrypt.org/) ist eine Zertifizierungsstelle, die kostenlose Zertifikate anbietet.</span><span class="sxs-lookup"><span data-stu-id="9740a-114">[Let's Encrypt](https://letsencrypt.org/) is a certificate authority that offers free certificates.</span></span>

<span data-ttu-id="9740a-115">In diesem Dokument werden [selbst signierte Entwicklungs Zertifikate](https://en.wikipedia.org/wiki/Self-signed_certificate) für das Hosting von vordefinierten Images verwendet `localhost` .</span><span class="sxs-lookup"><span data-stu-id="9740a-115">This document uses [self-signed development certificates](https://en.wikipedia.org/wiki/Self-signed_certificate) for hosting pre-built images over `localhost`.</span></span> <span data-ttu-id="9740a-116">Die Anweisungen ähneln der Verwendung von Produktions Zertifikaten.</span><span class="sxs-lookup"><span data-stu-id="9740a-116">The instructions are similar to using production certificates.</span></span>

<span data-ttu-id="9740a-117">Verwenden Sie [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) , um selbst signierte Zertifikate für Entwicklung und Tests zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9740a-117">Use [dotnet dev-certs](/dotnet/core/additional-tools/self-signed-certificates-guide) to create self-signed certificates for development and testing.</span></span>

<span data-ttu-id="9740a-118">Für produktionscerts:</span><span class="sxs-lookup"><span data-stu-id="9740a-118">For production certs:</span></span>

* <span data-ttu-id="9740a-119">Das `dotnet dev-certs` Tool ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9740a-119">The `dotnet dev-certs` tool is not required.</span></span>
* <span data-ttu-id="9740a-120">Zertifikate müssen nicht an dem Speicherort gespeichert werden, der in den Anweisungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9740a-120">Certificates do not need to be stored in the location used in the instructions.</span></span> <span data-ttu-id="9740a-121">Jeder Speicherort sollte funktionieren, obwohl das Speichern von Zertifikaten innerhalb Ihres Website Verzeichnisses nicht empfohlen wird.</span><span class="sxs-lookup"><span data-stu-id="9740a-121">Any location should work, although storing certs within your site directory is not recommended.</span></span>

<span data-ttu-id="9740a-122">Die Anweisungen im folgenden Abschnitt enthalten Informationen zum Einbinden von Zertifikaten in Container mithilfe der Docker- `-v` Befehlszeilenoption.</span><span class="sxs-lookup"><span data-stu-id="9740a-122">The instructions contained in the following section volume mount certificates into containers using Docker's `-v` command-line option.</span></span> <span data-ttu-id="9740a-123">Sie können Zertifikate in Container Images mit einem `COPY` Befehl in einer *dockerfile-Datei* hinzufügen, dies wird jedoch nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="9740a-123">You could add certificates into container images with a `COPY` command in a *Dockerfile*, but it's not recommended.</span></span> <span data-ttu-id="9740a-124">Das Kopieren von Zertifikaten in ein Abbild wird aus den folgenden Gründen nicht empfohlen:</span><span class="sxs-lookup"><span data-stu-id="9740a-124">Copying certificates into an image isn't recommended for the following reasons:</span></span>

* <span data-ttu-id="9740a-125">Es ist schwierig, das gleiche Image für Tests mit Entwickler Zertifikaten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9740a-125">It makes difficult to use the same image for testing with developer certificates.</span></span>
* <span data-ttu-id="9740a-126">Es ist schwierig, das gleiche Image für das Hosting mit Produktions Zertifikaten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9740a-126">It makes difficult to use the same image for Hosting with production certificates.</span></span>
* <span data-ttu-id="9740a-127">Es besteht ein erhebliches Risiko für die Offenlegung von Zertifikaten.</span><span class="sxs-lookup"><span data-stu-id="9740a-127">There is significant risk of certificate disclosure.</span></span>

## <a name="running-pre-built-container-images-with-https"></a><span data-ttu-id="9740a-128">Ausführen von vorgefertigten Container Images mit HTTPS</span><span class="sxs-lookup"><span data-stu-id="9740a-128">Running pre-built container images with HTTPS</span></span>

<span data-ttu-id="9740a-129">Verwenden Sie die folgenden Anweisungen für die Betriebssystem Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="9740a-129">Use the following instructions for your operating system configuration.</span></span>

### <a name="windows-using-linux-containers"></a><span data-ttu-id="9740a-130">Windows mithilfe von Linux-Containern</span><span class="sxs-lookup"><span data-stu-id="9740a-130">Windows using Linux containers</span></span>

<span data-ttu-id="9740a-131">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="9740a-131">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="9740a-132">Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="9740a-132">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="9740a-133">Führen Sie das Container Image mit ASP.net Core für HTTPS in einer Befehlsshell konfiguriert aus:</span><span class="sxs-lookup"><span data-stu-id="9740a-133">Run the container image with ASP.NET Core configured for HTTPS in a command shell:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="9740a-134">Wenn Sie [PowerShell](/powershell/scripting/overview)verwenden, ersetzen Sie `%USERPROFILE%` durch `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="9740a-134">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="9740a-135">Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9740a-135">The password must match the password used for the certificate.</span></span>


<span data-ttu-id="9740a-136">Hinweis: das Zertifikat muss in diesem Fall eine `.pfx` Datei sein.</span><span class="sxs-lookup"><span data-stu-id="9740a-136">Note: The certificate in this case must be a `.pfx` file.</span></span>  <span data-ttu-id="9740a-137">Das Verwenden einer- `.crt` oder- `.key` Datei mit oder ohne das Kennwort wird mit dem Beispiel Container nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9740a-137">Utilizing a `.crt` or `.key` file with or without the password isn't supported with the sample container.</span></span>  <span data-ttu-id="9740a-138">Wenn Sie z. b. eine `.crt` Datei angeben, gibt der Container möglicherweise Fehlermeldungen zurück, z. b. "der Server Modus-SSL muss ein Zertifikat mit dem zugeordneten privaten Schlüssel verwenden".</span><span class="sxs-lookup"><span data-stu-id="9740a-138">For example, when specifying a `.crt` file, the container may return error messages such as 'The server mode SSL must use a certificate with the associated private key.'.</span></span> <span data-ttu-id="9740a-139">Überprüfen Sie bei Verwendung von [WSL](/windows/wsl/about)den Bereitstellungspfad, um sicherzustellen, dass das Zertifikat ordnungsgemäß geladen</span><span class="sxs-lookup"><span data-stu-id="9740a-139">When using [WSL](/windows/wsl/about), validate the mount path to ensure that the certificate loads correctly.</span></span>

### <a name="macos-or-linux"></a><span data-ttu-id="9740a-140">macOS oder Linux</span><span class="sxs-lookup"><span data-stu-id="9740a-140">macOS or Linux</span></span>

<span data-ttu-id="9740a-141">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="9740a-141">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep ${HOME}/.aspnet/https/aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="9740a-142">`dotnet dev-certs https --trust` wird nur unter macOS und Windows unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9740a-142">`dotnet dev-certs https --trust` is only supported on macOS and Windows.</span></span> <span data-ttu-id="9740a-143">Sie müssen Zertifikate unter Linux in der von Ihrer Distribution unterstützten Weise als vertrauenswürdig einstufen.</span><span class="sxs-lookup"><span data-stu-id="9740a-143">You need to trust certs on Linux in the way that is supported by your distribution.</span></span> <span data-ttu-id="9740a-144">Es ist wahrscheinlich, dass Sie das Zertifikat in Ihrem Browser als vertrauenswürdig einstufen müssen.</span><span class="sxs-lookup"><span data-stu-id="9740a-144">It is likely that you need to trust the certificate in your browser.</span></span>

<span data-ttu-id="9740a-145">Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="9740a-145">In the preceding commands, replace `{ password here }` with a password.</span></span>

<span data-ttu-id="9740a-146">Führen Sie das Container Image mit für HTTPS konfigurierten ASP.net Core aus:</span><span class="sxs-lookup"><span data-stu-id="9740a-146">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetapp.pfx -v ${HOME}/.aspnet/https:/https/ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="9740a-147">Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9740a-147">The password must match the password used for the certificate.</span></span>

### <a name="windows-using-windows-containers"></a><span data-ttu-id="9740a-148">Windows mithilfe von Windows-Containern</span><span class="sxs-lookup"><span data-stu-id="9740a-148">Windows using Windows containers</span></span>

<span data-ttu-id="9740a-149">Zertifikat generieren und lokalen Computer konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="9740a-149">Generate certificate and configure local machine:</span></span>

```dotnetcli
dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p { password here }
dotnet dev-certs https --trust
```

<span data-ttu-id="9740a-150">Ersetzen Sie in den vorangehenden Befehlen `{ password here }` durch ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="9740a-150">In the preceding commands, replace `{ password here }` with a password.</span></span> <span data-ttu-id="9740a-151">Wenn Sie [PowerShell](/powershell/scripting/overview)verwenden, ersetzen Sie `%USERPROFILE%` durch `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="9740a-151">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>

<span data-ttu-id="9740a-152">Führen Sie das Container Image mit für HTTPS konfigurierten ASP.net Core aus:</span><span class="sxs-lookup"><span data-stu-id="9740a-152">Run the container image with ASP.NET Core configured for HTTPS:</span></span>

```console
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp
docker run --rm -it -p 8000:80 -p 8001:443 -e ASPNETCORE_URLS="https://+;http://+" -e ASPNETCORE_HTTPS_PORT=8001 -e ASPNETCORE_Kestrel__Certificates__Default__Password="password" -e ASPNETCORE_Kestrel__Certificates__Default__Path=\https\aspnetapp.pfx -v %USERPROFILE%\.aspnet\https:C:\https\ mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

<span data-ttu-id="9740a-153">Das Kennwort muss dem Kennwort entsprechen, das für das Zertifikat verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9740a-153">The password must match the password used for the certificate.</span></span> <span data-ttu-id="9740a-154">Wenn Sie [PowerShell](/powershell/scripting/overview)verwenden, ersetzen Sie `%USERPROFILE%` durch `$env:USERPROFILE` .</span><span class="sxs-lookup"><span data-stu-id="9740a-154">When using [PowerShell](/powershell/scripting/overview), replace `%USERPROFILE%` with `$env:USERPROFILE`.</span></span>
