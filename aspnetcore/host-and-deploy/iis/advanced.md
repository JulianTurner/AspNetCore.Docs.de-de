---
title: Erweiterte Konfiguration
author: rick-anderson
description: Erweiterte Konfiguration mit dem ASP.NET Core-Modul und den Internetinformationsdiensten (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058611"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a><span data-ttu-id="693be-103">Erweiterte Konfiguration des ASP.NET Core-Moduls und der IIS</span><span class="sxs-lookup"><span data-stu-id="693be-103">Advanced configuration of the ASP.NET Core Module and IIS</span></span>

<span data-ttu-id="693be-104">In diesem Artikel geht es um erweiterte Konfigurationsoptionen und entsprechende Szenarios für das ASP.NET Core-Modul und die IIS.</span><span class="sxs-lookup"><span data-stu-id="693be-104">This article covers advanced configuration options and scenarios for the ASP.NET Core Module and IIS.</span></span>

## <a name="modify-the-stack-size"></a><span data-ttu-id="693be-105">Ändern der Stapelgröße</span><span class="sxs-lookup"><span data-stu-id="693be-105">Modify the stack size</span></span>

<span data-ttu-id="693be-106">*Gilt nur bei Verwendung des In-Process-Hostingmodells.*</span><span class="sxs-lookup"><span data-stu-id="693be-106">*Only applies when using the in-process hosting model.*</span></span>

<span data-ttu-id="693be-107">Konfigurieren Sie die Größe des verwalteten Stapels mithilfe der `stackSize`-Einstellung in Byte in der `web.config`-Datei.</span><span class="sxs-lookup"><span data-stu-id="693be-107">Configure the managed stack size using the `stackSize` setting in bytes in the `web.config` file.</span></span> <span data-ttu-id="693be-108">Die Standardgröße beträgt 1.048.576 Byte (1 MB).</span><span class="sxs-lookup"><span data-stu-id="693be-108">The default size is 1,048,576 bytes (1 MB).</span></span> <span data-ttu-id="693be-109">Im folgenden Beispiel wird die Stapelgröße in 2 MB (2.097.152 Byte) geändert:</span><span class="sxs-lookup"><span data-stu-id="693be-109">The following example changes the stack size to 2 MB (2,097,152 bytes):</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a><span data-ttu-id="693be-110">Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken</span><span class="sxs-lookup"><span data-stu-id="693be-110">Proxy configuration uses HTTP protocol and a pairing token</span></span>

<span data-ttu-id="693be-111">*Gilt nur für Out-of-Process-Hosting.*</span><span class="sxs-lookup"><span data-stu-id="693be-111">*Only applies to out-of-process hosting.*</span></span>

<span data-ttu-id="693be-112">Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="693be-112">The proxy created between the ASP.NET Core Module and Kestrel uses the HTTP protocol.</span></span> <span data-ttu-id="693be-113">Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.</span><span class="sxs-lookup"><span data-stu-id="693be-113">There's no risk of eavesdropping the traffic between the module and Kestrel from a location off of the server.</span></span>

<span data-ttu-id="693be-114">Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen.</span><span class="sxs-lookup"><span data-stu-id="693be-114">A pairing token is used to guarantee that the requests received by Kestrel were proxied by IIS and didn't come from some other source.</span></span> <span data-ttu-id="693be-115">Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="693be-115">The pairing token is created and set into an environment variable (`ASPNETCORE_TOKEN`) by the module.</span></span> <span data-ttu-id="693be-116">Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="693be-116">The pairing token is also set into a header (`MS-ASPNETCORE-TOKEN`) on every proxied request.</span></span> <span data-ttu-id="693be-117">IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht.</span><span class="sxs-lookup"><span data-stu-id="693be-117">IIS Middleware checks each request it receives to confirm that the pairing token header value matches the environment variable value.</span></span> <span data-ttu-id="693be-118">Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="693be-118">If the token values are mismatched, the request is logged and rejected.</span></span> <span data-ttu-id="693be-119">Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="693be-119">The pairing token environment variable and the traffic between the module and Kestrel aren't accessible from a location off of the server.</span></span> <span data-ttu-id="693be-120">Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.</span><span class="sxs-lookup"><span data-stu-id="693be-120">Without knowing the pairing token value, an attacker can't submit requests that bypass the check in the IIS Middleware.</span></span>

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a><span data-ttu-id="693be-121">ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration</span><span class="sxs-lookup"><span data-stu-id="693be-121">ASP.NET Core Module with an IIS Shared Configuration</span></span>

<span data-ttu-id="693be-122">Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des `TrustedInstaller`-Kontos ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="693be-122">The ASP.NET Core Module installer runs with the privileges of the `TrustedInstaller` account.</span></span> <span data-ttu-id="693be-123">Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der `applicationHost.config`-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.</span><span class="sxs-lookup"><span data-stu-id="693be-123">Because the local system account doesn't have modify permission for the share path used by the IIS Shared Configuration, the installer throws an access denied error when attempting to configure the module settings in the `applicationHost.config` file on the share.</span></span>

<span data-ttu-id="693be-124">Wenn eine ISS-Freigabekonfiguration auf demselben Computer verwendet wird wie die ISS-Installation, führen Sie das Installationsprogramm des ASP.NET -Core-Hosting-Pakets mit auf `1` festgelegtem Parameter `OPT_NO_SHARED_CONFIG_CHECK` aus:</span><span class="sxs-lookup"><span data-stu-id="693be-124">When using an IIS Shared Configuration on the same machine as the IIS installation, run the ASP.NET Core Hosting Bundle installer with the `OPT_NO_SHARED_CONFIG_CHECK` parameter set to `1`:</span></span>

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

<span data-ttu-id="693be-125">Wenn sich der Pfad zur freigegebenen Konfiguration nicht auf demselben Computer wie die ISS-Installation befindet, befolgen Sie die folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="693be-125">When the path to the shared configuration isn't on the same machine as the IIS installation, follow these steps:</span></span>

1. <span data-ttu-id="693be-126">Deaktivieren Sie die IIS-Freigabekonfiguration.</span><span class="sxs-lookup"><span data-stu-id="693be-126">Disable the IIS Shared Configuration.</span></span>
1. <span data-ttu-id="693be-127">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="693be-127">Run the installer.</span></span>
1. <span data-ttu-id="693be-128">Exportieren Sie die aktualisierte `applicationHost.config`-Datei in die Dateifreigabe.</span><span class="sxs-lookup"><span data-stu-id="693be-128">Export the updated `applicationHost.config` file to the file share.</span></span>
1. <span data-ttu-id="693be-129">Aktivieren Sie die IIS-Freigabekonfiguration erneut.</span><span class="sxs-lookup"><span data-stu-id="693be-129">Re-enable the IIS Shared Configuration.</span></span>

## <a name="data-protection"></a><span data-ttu-id="693be-130">Schutz von Daten</span><span class="sxs-lookup"><span data-stu-id="693be-130">Data protection</span></span>

<span data-ttu-id="693be-131">Der [ASP.NET Core-Stapel zum Schutz von Daten](xref:security/data-protection/introduction) wird von mehreren ASP.NET-[Middlewarekomponenten](xref:fundamentals/middleware/index) genutzt, darunter auch von Middleware, die bei der Authentifizierung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="693be-131">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including middleware used in authentication.</span></span> <span data-ttu-id="693be-132">Selbst wenn die APIs zum Schutz von Daten nicht aus dem Benutzercode aufgerufen werden, sollte der Schutz von Daten mit einem Bereitstellungsskript oder im Benutzercode konfiguriert werden, um einen persistenten kryptografischen [Schlüsselspeicher](xref:security/data-protection/implementation/key-management) zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="693be-132">Even if Data Protection APIs aren't called by user code, data protection should be configured with a deployment script or in user code to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="693be-133">Wenn der Schutz von Daten nicht konfiguriert ist, werden die Schlüssel beim Neustarten der App im Arbeitsspeicher gespeichert und verworfen.</span><span class="sxs-lookup"><span data-stu-id="693be-133">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="693be-134">Falls die Schlüsselsammlung für den Datenschutz im Arbeitsspeicher gespeichert wird, wenn die App neu gestartet wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="693be-134">If the Data Protection key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="693be-135">Alle cookiebasierten Authentifizierungstoken werden für ungültig erklärt.</span><span class="sxs-lookup"><span data-stu-id="693be-135">All cookie-based authentication tokens are invalidated.</span></span> 
* <span data-ttu-id="693be-136">Benutzer müssen sich bei ihrer nächsten Anforderung erneut anmelden.</span><span class="sxs-lookup"><span data-stu-id="693be-136">Users are required to sign in again on their next request.</span></span> 
* <span data-ttu-id="693be-137">Alle mit dem Schlüsselbund geschützte Daten können nicht mehr entschlüsselt werden.</span><span class="sxs-lookup"><span data-stu-id="693be-137">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="693be-138">Dies kann [CSRF-Token](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) und [ASP.NET Core-MVC-TempData-cookies](xref:fundamentals/app-state#tempdata) einschließen.</span><span class="sxs-lookup"><span data-stu-id="693be-138">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="693be-139">Zum Konfigurieren des Schutzes von Daten unter IIS mithilfe des persistenten Schlüsselbunds verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="693be-139">To configure data protection under IIS to persist the key ring, use **one** of the following approaches:</span></span>

* <span data-ttu-id="693be-140">**Erstellen von Registrierungsschlüsseln für den Schutz von Daten**</span><span class="sxs-lookup"><span data-stu-id="693be-140">**Create Data Protection Registry keys**</span></span>

  <span data-ttu-id="693be-141">Schlüssel für den Schutz von Daten, die von ASP.NET Core-Apps verwendet werden, werden in der Registrierung außerhalb der Apps gespeichert.</span><span class="sxs-lookup"><span data-stu-id="693be-141">Data Protection keys used by ASP.NET Core apps are stored in the registry external to the apps.</span></span> <span data-ttu-id="693be-142">Sie müssen Registrierungsschlüssel für den App-Pool erstellen, um die Schlüssel für eine bestimmte App dauerhaft zu speichern.</span><span class="sxs-lookup"><span data-stu-id="693be-142">To persist the keys for a given app, create Registry keys for the app pool.</span></span>

  <span data-ttu-id="693be-143">Bei eigenständigen IIS-Installationen, die ohne Webfarm vorgesehen sind, kann das [PowerShell-Skript „Provision-AutoGenKeys.ps1“ für den Schutz von Daten](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) für jeden App-Pool genutzt werden, das mit einer ASP.NET Core-App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="693be-143">For standalone, non-webfarm IIS installations, the [Data Protection Provision-AutoGenKeys.ps1 PowerShell script](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) can be used for each app pool used with an ASP.NET Core app.</span></span> <span data-ttu-id="693be-144">Dieses Skript erstellt einen Registrierungsschlüssel in der HKLM-Registrierung, der nur für das Workerprozesskonto des App-Pools der App zugänglich ist.</span><span class="sxs-lookup"><span data-stu-id="693be-144">This script creates a Registry key in the HKLM registry that's accessible only to the worker process account of the app's app pool.</span></span> <span data-ttu-id="693be-145">Schlüssel werden in ruhendem Zustand mit DPAPI mit einem computerweiten Schlüssel verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="693be-145">Keys are encrypted at rest using DPAPI with a machine-wide key.</span></span>

  <span data-ttu-id="693be-146">In Webfarmszenarios kann eine App so konfiguriert werden, dass sie einen UNC-Pfad verwendet, um die Schlüsselsammlung für den Schutz von Daten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="693be-146">In web farm scenarios, an app can be configured to use a UNC path to store its Data Protection key ring.</span></span> <span data-ttu-id="693be-147">Standardmäßig werden die Schlüssel nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="693be-147">By default, the keys aren't encrypted.</span></span> <span data-ttu-id="693be-148">Stellen Sie sicher, dass die Dateiberechtigungen für die Netzwerkfreigabe auf das Windows-Konto beschränkt sind, mit dem die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="693be-148">Ensure that the file permissions for the network share are limited to the Windows account that the app runs under.</span></span> <span data-ttu-id="693be-149">Ein X.509-Zertifikat kann zum Schutz von Schlüsseln im ruhenden Zustand verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="693be-149">An X509 certificate can be used to protect keys at rest.</span></span> <span data-ttu-id="693be-150">Richten Sie ggf. einen Mechanismus ein, um es Benutzern zu ermöglichen, Zertifikate hochzuladen.</span><span class="sxs-lookup"><span data-stu-id="693be-150">Consider a mechanism to allow users to upload certificates.</span></span> <span data-ttu-id="693be-151">Platzieren Sie Zertifikate im Zertifikatspeicher des Benutzers für vertrauenswürdige Anbieter, und stellen Sie sicher, dass sie auf allen Computern verfügbar sind, auf denen die App des Benutzers ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="693be-151">Place certificates into the user's trusted certificate store and ensure they're available on all machines where the user's app runs.</span></span> <span data-ttu-id="693be-152">Weitere Informationen finden Sie unter <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="693be-152">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>

* <span data-ttu-id="693be-153">**Konfigurieren des IIS-Anwendungspools zum Laden des Benutzerprofils**</span><span class="sxs-lookup"><span data-stu-id="693be-153">**Configure the IIS Application Pool to load the user profile**</span></span>

  <span data-ttu-id="693be-154">Diese Einstellung befindet sich im Abschnitt **Prozessmodell** unter **Erweiterte Einstellungen** für den App-Pool.</span><span class="sxs-lookup"><span data-stu-id="693be-154">This setting is in the **Process Model** section under the **Advanced Settings** for the app pool.</span></span> <span data-ttu-id="693be-155">Legen Sie **Benutzerprofil laden** auf `True` fest.</span><span class="sxs-lookup"><span data-stu-id="693be-155">Set **Load User Profile** to `True`.</span></span> <span data-ttu-id="693be-156">Wenn diese Option auf `True` festgelegt ist, werden Schlüssel im Benutzerprofilverzeichnis gespeichert und mit DPAPI mit einem für das Benutzerkonto spezifischen Schlüssel geschützt.</span><span class="sxs-lookup"><span data-stu-id="693be-156">When set to `True`, keys are stored in the user profile directory and protected using DPAPI with a key specific to the user account.</span></span> <span data-ttu-id="693be-157">Schlüssel werden im Ordner `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="693be-157">Keys are persisted to the `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` folder.</span></span>

  <span data-ttu-id="693be-158">Das [`setProfileEnvironment`-Attribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) des App-Pools muss ebenfalls aktiviert sein.</span><span class="sxs-lookup"><span data-stu-id="693be-158">The app pool's [`setProfileEnvironment` attribute](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) must also be enabled.</span></span> <span data-ttu-id="693be-159">Der Standardwert von `setProfileEnvironment` ist `true`.</span><span class="sxs-lookup"><span data-stu-id="693be-159">The default value of `setProfileEnvironment` is `true`.</span></span> <span data-ttu-id="693be-160">In einigen Szenarien (z.B. Windows-Betriebssystem) ist `setProfileEnvironment` auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="693be-160">In some scenarios (for example, Windows OS), `setProfileEnvironment` is set to `false`.</span></span> <span data-ttu-id="693be-161">Gehen Sie folgendermaßen vor, wenn Schlüssel nicht wie erwartet im Benutzerprofilverzeichnis gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="693be-161">If keys aren't stored in the user profile directory as expected:</span></span>

  1. <span data-ttu-id="693be-162">Navigieren Sie zum Ordner `%windir%/system32/inetsrv/config`.</span><span class="sxs-lookup"><span data-stu-id="693be-162">Navigate to the `%windir%/system32/inetsrv/config` folder.</span></span>
  1. <span data-ttu-id="693be-163">Öffnen Sie die Datei `applicationHost.config` .</span><span class="sxs-lookup"><span data-stu-id="693be-163">Open the `applicationHost.config` file.</span></span>
  1. <span data-ttu-id="693be-164">Suchen Sie das Element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .</span><span class="sxs-lookup"><span data-stu-id="693be-164">Locate the `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` element.</span></span>
  1. <span data-ttu-id="693be-165">Bestätigen Sie, dass das `setProfileEnvironment`-Attribut nicht vorhanden ist, das standardmäßig den Wert `true` aufweist, oder legen Sie den Wert des Attributs explizit auf `true` fest.</span><span class="sxs-lookup"><span data-stu-id="693be-165">Confirm that the `setProfileEnvironment` attribute isn't present, which defaults the value to `true`, or explicitly set the attribute's value to `true`.</span></span>

* <span data-ttu-id="693be-166">**Verwenden des Dateisystems als Schlüsselbundspeicher**</span><span class="sxs-lookup"><span data-stu-id="693be-166">**Use the file system as a key ring store**</span></span>

  <span data-ttu-id="693be-167">Passen Sie den App-Code so an, dass er [das Dateisystem als Schlüsselbundspeicher verwendet](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="693be-167">Adjust the app code to [use the file system as a key ring store](xref:security/data-protection/configuration/overview).</span></span> <span data-ttu-id="693be-168">Verwenden Sie ein X.509-Zertifikat, um den Schlüsselbund zu schützen, und stellen Sie sicher, dass es sich bei dem Zertifikat um ein vertrauenswürdiges Zertifikat handelt.</span><span class="sxs-lookup"><span data-stu-id="693be-168">Use an X509 certificate to protect the key ring and ensure the certificate is a trusted certificate.</span></span> <span data-ttu-id="693be-169">Wenn es sich um ein selbstsigniertes Zertifikat handelt, müssen Sie es im vertrauenswürdigen Stammspeicher platzieren.</span><span class="sxs-lookup"><span data-stu-id="693be-169">If the certificate is self-signed, place the certificate in the Trusted Root store.</span></span>

  <span data-ttu-id="693be-170">Wenn IIS in einer Webfarm verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="693be-170">When using IIS in a web farm:</span></span>

  * <span data-ttu-id="693be-171">Verwenden Sie eine Dateifreigabe, auf die alle Computer zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="693be-171">Use a file share that all machines can access.</span></span>
  * <span data-ttu-id="693be-172">Stellen Sie ein X509-Zertifikat auf jedem Computer bereit.</span><span class="sxs-lookup"><span data-stu-id="693be-172">Deploy an X509 certificate to each machine.</span></span> <span data-ttu-id="693be-173">Konfigurieren Sie den [Schutz von Daten im Code](xref:security/data-protection/configuration/overview).</span><span class="sxs-lookup"><span data-stu-id="693be-173">Configure [Data Protection in code](xref:security/data-protection/configuration/overview).</span></span>

* <span data-ttu-id="693be-174">**Festlegen einer computerweiten Richtlinie für den Schutz von Daten**</span><span class="sxs-lookup"><span data-stu-id="693be-174">**Set a machine-wide policy for Data Protection**</span></span>

  <span data-ttu-id="693be-175">Das System zum Schutz von Daten verfügt über eine eingeschränkte Unterstützung zum Festlegen einer [computerweiten Standardrichtlinie](xref:security/data-protection/configuration/machine-wide-policy) für alle Apps, die die Datenschutz-APIs nutzen.</span><span class="sxs-lookup"><span data-stu-id="693be-175">The Data Protection system has limited support for setting a default [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy) for all apps that consume the Data Protection APIs.</span></span> <span data-ttu-id="693be-176">Weitere Informationen finden Sie unter <xref:security/data-protection/introduction>.</span><span class="sxs-lookup"><span data-stu-id="693be-176">For more information, see <xref:security/data-protection/introduction>.</span></span>

## <a name="iis-configuration"></a><span data-ttu-id="693be-177">IIS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="693be-177">IIS configuration</span></span>

<span data-ttu-id="693be-178">**Windows Server-Betriebssysteme**</span><span class="sxs-lookup"><span data-stu-id="693be-178">**Windows Server operating systems**</span></span>

<span data-ttu-id="693be-179">Aktivieren Sie die Serverrolle **Webserver (IIS)** , und richten Sie Rollendienste ein.</span><span class="sxs-lookup"><span data-stu-id="693be-179">Enable the **Web Server (IIS)** server role and establish role services.</span></span>

1. <span data-ttu-id="693be-180">Verwenden Sie den Assistenten **Rollen und Features hinzufügen** im Menü **Verwalten** oder den Link in **Server-Manager**.</span><span class="sxs-lookup"><span data-stu-id="693be-180">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span> <span data-ttu-id="693be-181">Aktivieren Sie im Schritt **Serverrollen** das Kontrollkästchen für **Webserver (IIS)** .</span><span class="sxs-lookup"><span data-stu-id="693be-181">On the **Server Roles** step, check the box for **Web Server (IIS)**.</span></span>

   ![Die Rolle „Webserver (IIS)“ wird im Schritt „Serverrollen auswählen“ ausgewählt.](index/_static/server-roles-ws2016.png)

1. <span data-ttu-id="693be-183">Nach dem Schritt **Features** wird der Schritt **Rollendienste** für Webserver (IIS) geladen.</span><span class="sxs-lookup"><span data-stu-id="693be-183">After the **Features** step, the **Role services** step loads for Web Server (IIS).</span></span> <span data-ttu-id="693be-184">Wählen Sie die gewünschten IIS-Rollendienste aus, oder übernehmen Sie die bereitgestellten Standardrollendienste.</span><span class="sxs-lookup"><span data-stu-id="693be-184">Select the IIS role services desired or accept the default role services provided.</span></span>

   ![Die Standardrollendienste werden im Schritt „Rollendienste auswählen“ ausgewählt.](index/_static/role-services-ws2016.png)

   <span data-ttu-id="693be-186">**Windows-Authentifizierung (optional)**</span><span class="sxs-lookup"><span data-stu-id="693be-186">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="693be-187">Um die Windows-Authentifizierung zu aktivieren, erweitern Sie die folgenden Knoten: **Webserver** > **Sicherheit**.</span><span class="sxs-lookup"><span data-stu-id="693be-187">To enable Windows Authentication, expand the following nodes: **Web Server** > **Security**.</span></span> <span data-ttu-id="693be-188">Wählen Sie das Feature **Windows-Authentifizierung** aus.</span><span class="sxs-lookup"><span data-stu-id="693be-188">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="693be-189">Weitere Informationen finden Sie unter [Windows-Authentifizierung `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) und [Konfigurieren der Windows-Authentifizierung](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="693be-189">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="693be-190">**WebSockets (optional)**</span><span class="sxs-lookup"><span data-stu-id="693be-190">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="693be-191">WebSockets wird mit ASP.NET Core 1.1 oder höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="693be-191">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="693be-192">Um WebSockets zu aktivieren, erweitern Sie die folgenden Knoten: **Webserver** > **Anwendungsentwicklung**.</span><span class="sxs-lookup"><span data-stu-id="693be-192">To enable WebSockets, expand the following nodes: **Web Server** > **Application Development**.</span></span> <span data-ttu-id="693be-193">Wählen Sie das Feature **WebSocket-Protokoll** aus.</span><span class="sxs-lookup"><span data-stu-id="693be-193">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="693be-194">Weitere Informationen finden Sie unter [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="693be-194">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="693be-195">Fahren Sie mit dem Schritt **Bestätigung** fort, um die Webserverrolle und die Dienste zu installieren.</span><span class="sxs-lookup"><span data-stu-id="693be-195">Proceed through the **Confirmation** step to install the web server role and services.</span></span> <span data-ttu-id="693be-196">Ein Server-/IIS-Neustart ist nach der Installation der Rolle **Webserver (IIS)** nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="693be-196">A server/IIS restart isn't required after installing the **Web Server (IIS)** role.</span></span>

<span data-ttu-id="693be-197">**Windows-Desktopbetriebssysteme**</span><span class="sxs-lookup"><span data-stu-id="693be-197">**Windows desktop operating systems**</span></span>

<span data-ttu-id="693be-198">Aktivieren Sie die **IIS-Verwaltungskonsole** und die **WWW-Dienste**.</span><span class="sxs-lookup"><span data-stu-id="693be-198">Enable the **IIS Management Console** and **World Wide Web Services**.</span></span>

1. <span data-ttu-id="693be-199">Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="693be-199">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

1. <span data-ttu-id="693be-200">Öffnen Sie den Knoten **Internetinformationsdienste**.</span><span class="sxs-lookup"><span data-stu-id="693be-200">Open the **Internet Information Services** node.</span></span> <span data-ttu-id="693be-201">Öffnen Sie den Knoten **Webverwaltungstools**.</span><span class="sxs-lookup"><span data-stu-id="693be-201">Open the **Web Management Tools** node.</span></span>

1. <span data-ttu-id="693be-202">Aktivieren Sie das Kontrollkästchen für **IIS-Verwaltungskonsole**.</span><span class="sxs-lookup"><span data-stu-id="693be-202">Check the box for **IIS Management Console**.</span></span>

1. <span data-ttu-id="693be-203">Aktivieren Sie das Kontrollkästchen für **WWW-Dienste**.</span><span class="sxs-lookup"><span data-stu-id="693be-203">Check the box for **World Wide Web Services**.</span></span>

1. <span data-ttu-id="693be-204">Akzeptieren Sie die Standardfeatures für **WWW-Dienste** , oder passen Sie die IIS-Features an.</span><span class="sxs-lookup"><span data-stu-id="693be-204">Accept the default features for **World Wide Web Services** or customize the IIS features.</span></span>

   <span data-ttu-id="693be-205">**Windows-Authentifizierung (optional)**</span><span class="sxs-lookup"><span data-stu-id="693be-205">**Windows Authentication (Optional)**</span></span>  
   <span data-ttu-id="693be-206">Um die Windows-Authentifizierung zu aktivieren, erweitern Sie die folgenden Knoten: **WWW-Dienste** > **Sicherheit**.</span><span class="sxs-lookup"><span data-stu-id="693be-206">To enable Windows Authentication, expand the following nodes: **World Wide Web Services** > **Security**.</span></span> <span data-ttu-id="693be-207">Wählen Sie das Feature **Windows-Authentifizierung** aus.</span><span class="sxs-lookup"><span data-stu-id="693be-207">Select the **Windows Authentication** feature.</span></span> <span data-ttu-id="693be-208">Weitere Informationen finden Sie unter [Windows-Authentifizierung `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) und [Konfigurieren der Windows-Authentifizierung](xref:security/authentication/windowsauth).</span><span class="sxs-lookup"><span data-stu-id="693be-208">For more information, see [Windows Authentication `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) and [Configure Windows authentication](xref:security/authentication/windowsauth).</span></span>

   <span data-ttu-id="693be-209">**WebSockets (optional)**</span><span class="sxs-lookup"><span data-stu-id="693be-209">**WebSockets (Optional)**</span></span>  
   <span data-ttu-id="693be-210">WebSockets wird mit ASP.NET Core 1.1 oder höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="693be-210">WebSockets is supported with ASP.NET Core 1.1 or later.</span></span> <span data-ttu-id="693be-211">Um WebSockets zu aktivieren, erweitern Sie die folgenden Knoten: **WWW-Dienste** > **Anwendungsentwicklungsfeatures**.</span><span class="sxs-lookup"><span data-stu-id="693be-211">To enable WebSockets, expand the following nodes: **World Wide Web Services** > **Application Development Features**.</span></span> <span data-ttu-id="693be-212">Wählen Sie das Feature **WebSocket-Protokoll** aus.</span><span class="sxs-lookup"><span data-stu-id="693be-212">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="693be-213">Weitere Informationen finden Sie unter [WebSockets](xref:fundamentals/websockets).</span><span class="sxs-lookup"><span data-stu-id="693be-213">For more information, see [WebSockets](xref:fundamentals/websockets).</span></span>

1. <span data-ttu-id="693be-214">Wenn die IIS-Installation einen Neustart erfordert, starten Sie das System neu.</span><span class="sxs-lookup"><span data-stu-id="693be-214">If the IIS installation requires a restart, restart the system.</span></span>

![Die IIS-Verwaltungskonsole und WWW-Dienste werden in Windows-Features ausgewählt.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a><span data-ttu-id="693be-216">Virtuelle Verzeichnisse</span><span class="sxs-lookup"><span data-stu-id="693be-216">Virtual Directories</span></span>

<span data-ttu-id="693be-217">[Virtuelle IIS-Verzeichnisse](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) werden mit ASP.NET Core-Apps nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="693be-217">[IIS Virtual Directories](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) aren't supported with ASP.NET Core apps.</span></span> <span data-ttu-id="693be-218">Eine App kann als [untergeordnete Anwendung](#sub-applications) gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="693be-218">An app can be hosted as a [sub-application](#sub-applications).</span></span>

## <a name="sub-applications"></a><span data-ttu-id="693be-219">Untergeordnete Anwendungen</span><span class="sxs-lookup"><span data-stu-id="693be-219">Sub-applications</span></span>

<span data-ttu-id="693be-220">Eine ASP.NET Core-App kann als [untergeordnete IIS-Anwendung (untergeordnete App)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="693be-220">An ASP.NET Core app can be hosted as an [IIS sub-application (sub-app)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications).</span></span> <span data-ttu-id="693be-221">Der Pfad der untergeordneten App wird ein Teil der URL der Stamm-App.</span><span class="sxs-lookup"><span data-stu-id="693be-221">The sub-app's path becomes part of the root app's URL.</span></span>

<span data-ttu-id="693be-222">Statische Assetlinks in der untergeordneten App sollten die Tilde/Schrägstrich-Notation verwenden (`~/`).</span><span class="sxs-lookup"><span data-stu-id="693be-222">Static asset links within the sub-app should use tilde-slash (`~/`) notation.</span></span> <span data-ttu-id="693be-223">Die Tilde/Schrägstrich-Notation löst ein [Taghilfsprogramm](xref:mvc/views/tag-helpers/intro) aus, um die Pfadbasis der untergeordneten App dem gerenderten relativen Link voranzustellen.</span><span class="sxs-lookup"><span data-stu-id="693be-223">Tilde-slash notation triggers a [Tag Helper](xref:mvc/views/tag-helpers/intro) to prepend the sub-app's pathbase to the rendered relative link.</span></span> <span data-ttu-id="693be-224">Für eine untergeordnete App unter `/subapp_path` wird ein mit `src="~/image.png"` verknüpftes Bild als `src="/subapp_path/image.png"` gerendert.</span><span class="sxs-lookup"><span data-stu-id="693be-224">For a sub-app at `/subapp_path`, an image linked with `src="~/image.png"` is rendered as `src="/subapp_path/image.png"`.</span></span> <span data-ttu-id="693be-225">Die Static File Middleware verarbeitet die Anforderung der statischen Datei nicht.</span><span class="sxs-lookup"><span data-stu-id="693be-225">The root app's Static File Middleware doesn't process the static file request.</span></span> <span data-ttu-id="693be-226">Die Anforderung wird von der Static File Middleware der untergeordneten App verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="693be-226">The request is processed by the sub-app's Static File Middleware.</span></span>

<span data-ttu-id="693be-227">Wenn das `src`-Attribut eines statischen Objekts auf einen absoluten Pfad festgelegt wird (z.B. `src="/image.png"`), wird der Link ohne die Pfadbasis der untergeordneten App gerendert.</span><span class="sxs-lookup"><span data-stu-id="693be-227">If a static asset's `src` attribute is set to an absolute path (for example, `src="/image.png"`), the link is rendered without the sub-app's pathbase.</span></span> <span data-ttu-id="693be-228">Die Middleware für statische Dateien der Stamm-App versucht, das Objekt vom [Webstamm](xref:fundamentals/index#web-root) der Stamm-App aus bereitzustellen, was zu einer *404 Nicht gefunden* -Antwort führt, solange das statische Objekt in der Stamm-App nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="693be-228">The root app's Static File Middleware attempts to serve the asset from the root app's [web root](xref:fundamentals/index#web-root), which results in a *404 - Not Found* response unless the static asset is available from the root app.</span></span>

<span data-ttu-id="693be-229">So hosten Sie eine ASP.NET Core-App als untergeordnete App unter einer anderen ASP.NET Core-App:</span><span class="sxs-lookup"><span data-stu-id="693be-229">To host an ASP.NET Core app as a sub-app under another ASP.NET Core app:</span></span>

1. <span data-ttu-id="693be-230">Richten Sie einen App-Pool für die untergeordnete App ein.</span><span class="sxs-lookup"><span data-stu-id="693be-230">Establish an app pool for the sub-app.</span></span> <span data-ttu-id="693be-231">Legen Sie die **.NET CLR-Version** auf **Kein verwalteter Code** fest, weil die Core Common Language Runtime (CoreCLR) für .NET Core gestartet wird, um die App im Workerprozess zu hosten, nicht der Desktop-CLR (.NET CLR).</span><span class="sxs-lookup"><span data-stu-id="693be-231">Set the **.NET CLR Version** to **No Managed Code** because the Core Common Language Runtime (CoreCLR) for .NET Core is booted to host the app in the worker process, not the desktop CLR (.NET CLR).</span></span>

1. <span data-ttu-id="693be-232">Fügen Sie die Stammwebsite im IIS-Manager mit der untergeordneten App in einem unter der Stammwebsite liegenden Ordner hinzu.</span><span class="sxs-lookup"><span data-stu-id="693be-232">Add the root site in IIS Manager with the sub-app in a folder under the root site.</span></span>

1. <span data-ttu-id="693be-233">Klicken Sie mit der rechten Maustaste im IIS-Manager auf den Ordner der untergeordneten App, und wählen Sie **In Anwendung konvertieren** aus.</span><span class="sxs-lookup"><span data-stu-id="693be-233">Right-click the sub-app folder in IIS Manager and select **Convert to Application**.</span></span>

1. <span data-ttu-id="693be-234">Weisen Sie im Dialogfeld **Anwendung hinzufügen** mit der Schaltfläche **Auswählen** den **Anwendungspool** dem App-Pool zu, den Sie für die untergeordnete App erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="693be-234">In the **Add Application** dialog, use the **Select** button for the **Application Pool** to assign the app pool that you created for the sub-app.</span></span> <span data-ttu-id="693be-235">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="693be-235">Select **OK**.</span></span>

<span data-ttu-id="693be-236">Die Zuweisung eines separaten App-Pools zur untergeordneten App ist eine Anforderung, wenn Sie das In-Process-Hostingmodell verwenden.</span><span class="sxs-lookup"><span data-stu-id="693be-236">The assignment of a separate app pool to the sub-app is a requirement when using the in-process hosting model.</span></span>

<span data-ttu-id="693be-237">Weitere Informationen zum In-Process-Hostingmodell und Konfigurieren des ASP.NET Core-Moduls finden Sie unter <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="693be-237">For more information on the in-process hosting model and configuring the ASP.NET Core Module, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="application-pools"></a><span data-ttu-id="693be-238">Anwendungspools</span><span class="sxs-lookup"><span data-stu-id="693be-238">Application Pools</span></span>

<span data-ttu-id="693be-239">Die App-Poolisolation wird durch das Hostingmodell bestimmt.</span><span class="sxs-lookup"><span data-stu-id="693be-239">App pool isolation is determined by the hosting model:</span></span>

* <span data-ttu-id="693be-240">In-Process-Hosting: Apps müssen in separaten App-Pools ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="693be-240">In-process hosting: Apps are required to run in separate app pools.</span></span>
* <span data-ttu-id="693be-241">Out-of-Process-Hosting: Es wird empfohlen, die Apps voneinander zu isolieren, indem Sie jede App in ihrem eigenen App-Pool ausführen.</span><span class="sxs-lookup"><span data-stu-id="693be-241">Out-of-process hosting: We recommend isolating the apps from each other by running each app in its own app pool.</span></span>

<span data-ttu-id="693be-242">Im IIS-Dialogfeld **Website hinzufügen** wird standardmäßig ein einzelner App-Pool pro App eingesetzt.</span><span class="sxs-lookup"><span data-stu-id="693be-242">The IIS **Add Website** dialog defaults to a single app pool per app.</span></span> <span data-ttu-id="693be-243">Wenn ein **Websitename** angegeben ist, wird der Text automatisch in das Textfeld **Anwendungspool** übertragen.</span><span class="sxs-lookup"><span data-stu-id="693be-243">When a **Site name** is provided, the text is automatically transferred to the **Application pool** textbox.</span></span> <span data-ttu-id="693be-244">Ein neuer App-Pool mit dem Namen der Website wird erstellt, wenn die Website hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="693be-244">A new app pool is created using the site name when the site is added.</span></span>

## <a name="application-pool-no-locidentity"></a><span data-ttu-id="693be-245">Anwendungspool Identity</span><span class="sxs-lookup"><span data-stu-id="693be-245">Application Pool Identity</span></span>

<span data-ttu-id="693be-246">Mit einem Konto für die Identität des App-Pools können Sie eine App unter einem eindeutigen Konto ausführen, ohne Domänen oder lokale Konten erstellen und verwalten zu müssen.</span><span class="sxs-lookup"><span data-stu-id="693be-246">An app pool identity account allows an app to run under a unique account without having to create and manage domains or local accounts.</span></span> <span data-ttu-id="693be-247">Unter IIS 8.0 oder höher erstellt der IIS-Administratorworkerprozess (WAS) ein virtuelles Konto mit dem Namen des neuen App-Pools und führt die Workerprozesse des App-Pools standardmäßig unter diesem Konto aus.</span><span class="sxs-lookup"><span data-stu-id="693be-247">On IIS 8.0 or later, the IIS Admin Worker Process (WAS) creates a virtual account with the name of the new app pool and runs the app pool's worker processes under this account by default.</span></span> <span data-ttu-id="693be-248">Stellen Sie sicher, dass in der IIS-Verwaltungskonsole unter **Erweiterte Einstellungen** für den App-Pool die Option **Identity** auf `ApplicationPoolIdentity` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="693be-248">In the IIS Management Console under **Advanced Settings** for the app pool, ensure that the **Identity** is set to use `ApplicationPoolIdentity`:</span></span>

![Dialogfeld „Erweiterte Einstellungen“ für den Anwendungspool](index/_static/apppool-identity.png)

<span data-ttu-id="693be-250">Der IIS-Verwaltungsprozess erstellt im Windows-Sicherheitssystem einen sicheren Bezeichner mit dem Namen des App-Pools.</span><span class="sxs-lookup"><span data-stu-id="693be-250">The IIS management process creates a secure identifier with the name of the app pool in the Windows Security System.</span></span> <span data-ttu-id="693be-251">Ressourcen können mithilfe dieser Identität geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="693be-251">Resources can be secured using this identity.</span></span> <span data-ttu-id="693be-252">Allerdings ist diese Identität kein echtes Benutzerkonto und wird in der Windows-Benutzerverwaltungskonsole nicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="693be-252">However, this identity isn't a real user account and doesn't show up in the Windows User Management Console.</span></span>

<span data-ttu-id="693be-253">Wenn der IIS-Workerprozess erhöhte Rechte für den Zugriff auf Ihre Anwendung erfordert, ändern Sie die Zugriffssteuerungsliste (ACL) für das Verzeichnis mit der App:</span><span class="sxs-lookup"><span data-stu-id="693be-253">If the IIS worker process requires elevated access to the app, modify the Access Control List (ACL) for the directory containing the app:</span></span>

1. <span data-ttu-id="693be-254">Öffnen Sie Windows Explorer, und navigieren Sie zum Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="693be-254">Open Windows Explorer and navigate to the directory.</span></span>

1. <span data-ttu-id="693be-255">Klicken Sie mit der rechten Maustaste auf das Verzeichnis, und klicken Sie auf **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="693be-255">Right-click on the directory and select **Properties**.</span></span>

1. <span data-ttu-id="693be-256">Klicken Sie auf der Registerkarte **Sicherheit** auf die Schaltfläche **Bearbeiten** und dann auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="693be-256">Under the **Security** tab, select the **Edit** button and then the **Add** button.</span></span>

1. <span data-ttu-id="693be-257">Wählen Sie die Schaltfläche **Speicherorte** aus, und stellen Sie sicher, dass das System ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="693be-257">Select the **Locations** button and make sure the system is selected.</span></span>

1. <span data-ttu-id="693be-258">Geben Sie im Bereich **Geben Sie die Namen der auszuwählenden Objekte ein** das `IIS AppPool\{APP POOL NAME}`-Format ein. Hierbei steht der Platzhalter `{APP POOL NAME}` für den Namen des App-Pools.</span><span class="sxs-lookup"><span data-stu-id="693be-258">Enter `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, in **Enter the object names to select** area.</span></span> <span data-ttu-id="693be-259">Klicken Sie auf die Schaltfläche **Namen überprüfen**.</span><span class="sxs-lookup"><span data-stu-id="693be-259">Select the **Check Names** button.</span></span> <span data-ttu-id="693be-260">Überprüfen Sie für *DefaultAppPool* die Namen mit `IIS AppPool\DefaultAppPool`.</span><span class="sxs-lookup"><span data-stu-id="693be-260">For the *DefaultAppPool* check the names using `IIS AppPool\DefaultAppPool`.</span></span> <span data-ttu-id="693be-261">Bei Auswahl der Schaltfläche **Namen überprüfen** wird im Bereich für Objektnamen der Wert `DefaultAppPool` angegeben.</span><span class="sxs-lookup"><span data-stu-id="693be-261">When the **Check Names** button is selected, a value of `DefaultAppPool` is indicated in the object names area.</span></span> <span data-ttu-id="693be-262">Es ist nicht möglich, den Namen des App-Pools direkt in den Bereich für Objektnamen einzugeben.</span><span class="sxs-lookup"><span data-stu-id="693be-262">It isn't possible to enter the app pool name directly into the object names area.</span></span> <span data-ttu-id="693be-263">Verwenden Sie das Format `IIS AppPool\{APP POOL NAME}`, wenn Sie den Objektnamen überprüfen. Hierbei steht der Platzhalter `{APP POOL NAME}` für den Namen des App-Pools.</span><span class="sxs-lookup"><span data-stu-id="693be-263">Use the `IIS AppPool\{APP POOL NAME}` format, where the placeholder `{APP POOL NAME}` is the app pool name, when checking for the object name.</span></span>

   ![Dialogfeld „Benutzer oder Gruppen auswählen“ für den App-Ordner: Der Name des App-Pools, „DefaultAppPool“, wird an „IIS AppPool\"“ im Bereich der Objektnamen angehängt, bevor „Namen überprüfen“ ausgewählt wird.](index/_static/select-users-or-groups-1.png)

1. <span data-ttu-id="693be-265">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="693be-265">Select **OK**.</span></span>

   ![Dialogfeld „Benutzer oder Gruppen auswählen“ für den App-Ordner: Nach Auswahl von „Namen überprüfen“ wird der Objektname „DefaultAppPool“ im Bereich der Objektnamen angezeigt.](index/_static/select-users-or-groups-2.png)

1. <span data-ttu-id="693be-267">Standardmäßig sollten Lese- und Schreibberechtigungen gewährt werden.</span><span class="sxs-lookup"><span data-stu-id="693be-267">Read &amp; execute permissions should be granted by default.</span></span> <span data-ttu-id="693be-268">Erteilen Sie weitere Berechtigungen, sofern erforderlich.</span><span class="sxs-lookup"><span data-stu-id="693be-268">Provide additional permissions as needed.</span></span>

<span data-ttu-id="693be-269">Zugriff kann auch über eine Eingabeaufforderung mit dem Tool **ICACLS** gewährt werden.</span><span class="sxs-lookup"><span data-stu-id="693be-269">Access can also be granted at a command prompt using the **ICACLS** tool.</span></span> <span data-ttu-id="693be-270">Im folgenden Befehl wird als Beispiel *DefaultAppPool* verwendet:</span><span class="sxs-lookup"><span data-stu-id="693be-270">Using the *DefaultAppPool* as an example, the following command is used:</span></span>

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

<span data-ttu-id="693be-271">Weitere Informationen finden Sie im Thema [icacls](/windows-server/administration/windows-commands/icacls).</span><span class="sxs-lookup"><span data-stu-id="693be-271">For more information, see the [icacls](/windows-server/administration/windows-commands/icacls) topic.</span></span>

## <a name="http2-support"></a><span data-ttu-id="693be-272">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="693be-272">HTTP/2 support</span></span>

<span data-ttu-id="693be-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird mit ASP.NET Core in den folgenden IIS-Bereitstellungsszenarien unterstützt:</span><span class="sxs-lookup"><span data-stu-id="693be-273">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="693be-274">In-Process</span><span class="sxs-lookup"><span data-stu-id="693be-274">In-process</span></span>
  * <span data-ttu-id="693be-275">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="693be-275">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="693be-276">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="693be-276">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="693be-277">Out-of-Process</span><span class="sxs-lookup"><span data-stu-id="693be-277">Out-of-process</span></span>
  * <span data-ttu-id="693be-278">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="693be-278">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="693be-279">Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit dem [Kestrel-Server](xref:fundamentals/servers/kestrel) verwendet HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="693be-279">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>
  * <span data-ttu-id="693be-280">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="693be-280">TLS 1.2 or later connection</span></span>

<span data-ttu-id="693be-281">Für eine In-Process-Bereitstellung, wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) den Wert `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="693be-281">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="693be-282">Für eine Out-of-Process-Bereitstellung, wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) den Wert `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="693be-282">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="693be-283">Weitere Informationen zu den In-Process- und Out-of-Process-Hostingmodellen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="693be-283">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="693be-284">HTTP/2 ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="693be-284">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="693be-285">Verbindungen führen ein Fallback auf HTTP/1.1 aus, wenn keine HTTP/2-Verbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="693be-285">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="693be-286">Weitere Informationen zur HTTP/2-Konfiguration mit IIS-Bereitstellungen finden Sie unter [HTTP/2 unter IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="693be-286">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="cors-preflight-requests"></a><span data-ttu-id="693be-287">CORS-Preflightanforderungen</span><span class="sxs-lookup"><span data-stu-id="693be-287">CORS preflight requests</span></span>

<span data-ttu-id="693be-288">*Dieser Abschnitt gilt nur für ASP.NET Core-Apps, die auf das .NET Framework ausgerichtet sind.*</span><span class="sxs-lookup"><span data-stu-id="693be-288">*This section only applies to ASP.NET Core apps that target the .NET Framework.*</span></span>

<span data-ttu-id="693be-289">Für eine ASP.NET Core-App, die auf das .NET Framework ausgerichtet ist, werden OPTIONS-Anforderungen in IIS standardmäßig nicht an die App übergeben.</span><span class="sxs-lookup"><span data-stu-id="693be-289">For an ASP.NET Core app that targets the .NET Framework, OPTIONS requests aren't passed to the app by default in IIS.</span></span> <span data-ttu-id="693be-290">Informationen dazu, wie Sie die IIS-Handler der App in `web.config` so konfigurieren, dass OPTIONS-Anforderungen übergeben werden, finden Sie unter [Aktivieren ursprungsübergreifender Anforderungen in ASP.NET-Web-API 2: Funktionsweise von CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span><span class="sxs-lookup"><span data-stu-id="693be-290">To learn how to configure the app's IIS handlers in `web.config` to pass OPTIONS requests, see [Enable cross-origin requests in ASP.NET Web API 2: How CORS Works](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).</span></span>

## <a name="application-initialization-module-and-idle-timeout"></a><span data-ttu-id="693be-291">Anwendungsinitialisierungsmodul und Leerlauftimeout</span><span class="sxs-lookup"><span data-stu-id="693be-291">Application Initialization Module and Idle Timeout</span></span>

<span data-ttu-id="693be-292">Bei Hosting in IIS durch Version 2 das ASP.NET Core-Moduls:</span><span class="sxs-lookup"><span data-stu-id="693be-292">When hosted in IIS by the ASP.NET Core Module version 2:</span></span>

* <span data-ttu-id="693be-293">[Anwendungsinitialisierungsmodul:](#application-initialization-module) App-Hostings vom Typ [In-Process](xref:host-and-deploy/iis/in-process-hosting) oder [Out-of-Process](xref:host-and-deploy/iis/out-of-process-hosting) können so konfiguriert werden, dass sie automatisch im Rahmen eines Workerprozessneustarts oder eines Serverneustarts gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="693be-293">[Application Initialization Module](#application-initialization-module): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) or [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) can be configured to start automatically on a worker process restart or server restart.</span></span>
* <span data-ttu-id="693be-294">[Leerlauftimeout:](#idle-timeout) App-Hostings vom Typ [In-Process](xref:host-and-deploy/iis/in-process-hosting) können so konfiguriert werden, dass in Zeiten ohne Aktivität kein Timeout eintritt.</span><span class="sxs-lookup"><span data-stu-id="693be-294">[Idle Timeout](#idle-timeout): App's hosted [in-process](xref:host-and-deploy/iis/in-process-hosting) can be configured not to time out during periods of inactivity.</span></span>

### <a name="application-initialization-module"></a><span data-ttu-id="693be-295">Anwendungsinitialisierungsmodul</span><span class="sxs-lookup"><span data-stu-id="693be-295">Application Initialization Module</span></span>

<span data-ttu-id="693be-296">*Gilt für In-Process und Out-of-Process gehostete Apps.*</span><span class="sxs-lookup"><span data-stu-id="693be-296">*Applies to apps hosted in-process and out-of-process.*</span></span>

<span data-ttu-id="693be-297">[IIS-Anwendungsinitialisierung](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) ist ein IIS-Feature, das eine HTTP-Anforderung an die App sendet, wenn der App-Pool startet oder wiederverwendet wird.</span><span class="sxs-lookup"><span data-stu-id="693be-297">[IIS Application Initialization](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) is an IIS feature that sends an HTTP request to the app when the app pool starts or is recycled.</span></span> <span data-ttu-id="693be-298">Die Anforderung löst den Start der App aus.</span><span class="sxs-lookup"><span data-stu-id="693be-298">The request triggers the app to start.</span></span> <span data-ttu-id="693be-299">Standardmäßig gibt IIS eine Anforderung an die Stamm-URL der App (`/`) zum Initialisieren der App aus (weitere Informationen zur Konfiguration finden Sie unter [Zusätzliche Ressourcen](#application-initialization-module-and-idle-timeout-additional-resources)).</span><span class="sxs-lookup"><span data-stu-id="693be-299">By default, IIS issues a request to the app's root URL (`/`) to initialize the app (see the [additional resources](#application-initialization-module-and-idle-timeout-additional-resources) for more details on configuration).</span></span>

<span data-ttu-id="693be-300">Vergewissern Sie sich, dass die IIS-Anwendungsinitialisierungs-Rollenfunktion aktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="693be-300">Confirm that the IIS Application Initialization role feature in enabled:</span></span>

<span data-ttu-id="693be-301">Unter Windows 7 oder höher gilt für Desktopsysteme bei lokaler Verwendung von IIS:</span><span class="sxs-lookup"><span data-stu-id="693be-301">On Windows 7 or later desktop systems when using IIS locally:</span></span>

1. <span data-ttu-id="693be-302">Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="693be-302">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="693be-303">Öffnen Sie **Internetinformationsdienste** > **WWW-Dienste** > **Anwendungsentwicklungsfeatures**.</span><span class="sxs-lookup"><span data-stu-id="693be-303">Open **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="693be-304">Aktivieren Sie das Kontrollkästchen für **Anwendungsinitialisierung**.</span><span class="sxs-lookup"><span data-stu-id="693be-304">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="693be-305">Unter Windows Server 2008 R2 oder höher:</span><span class="sxs-lookup"><span data-stu-id="693be-305">On Windows Server 2008 R2 or later:</span></span>

1. <span data-ttu-id="693be-306">Öffnen Sie den **Assistenten zum Hinzufügen von Rollen und Features**.</span><span class="sxs-lookup"><span data-stu-id="693be-306">Open the **Add Roles and Features Wizard**.</span></span>
1. <span data-ttu-id="693be-307">Öffnen Sie im Bereich **Rollendienste auswählen** den Knoten **Anwendungsentwicklung**.</span><span class="sxs-lookup"><span data-stu-id="693be-307">In the **Select role services** panel, open the **Application Development** node.</span></span>
1. <span data-ttu-id="693be-308">Aktivieren Sie das Kontrollkästchen für **Anwendungsinitialisierung**.</span><span class="sxs-lookup"><span data-stu-id="693be-308">Select the check box for **Application Initialization**.</span></span>

<span data-ttu-id="693be-309">Verwenden Sie einen der folgenden Ansätze, um das Anwendungsinitialisierungsmodul für die Website zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="693be-309">Use either of the following approaches to enable the Application Initialization Module for the site:</span></span>

* <span data-ttu-id="693be-310">Bei Verwenden von IIS-Manager:</span><span class="sxs-lookup"><span data-stu-id="693be-310">Using IIS Manager:</span></span>

  1. <span data-ttu-id="693be-311">Wählen Sie **Anwendungspools** im Bereich **Verbindungen** aus.</span><span class="sxs-lookup"><span data-stu-id="693be-311">Select **Application Pools** in the **Connections** panel.</span></span>
  1. <span data-ttu-id="693be-312">Klicken Sie mit der rechten Maustaste im App-Pool der App in die Liste, und wählen Sie **Erweiterte Einstellungen** aus.</span><span class="sxs-lookup"><span data-stu-id="693be-312">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
  1. <span data-ttu-id="693be-313">Der standardmäßige **Startmodus** ist `OnDemand`.</span><span class="sxs-lookup"><span data-stu-id="693be-313">The default **Start Mode** is `OnDemand`.</span></span> <span data-ttu-id="693be-314">Legen Sie den **Startmodus** auf `AlwaysRunning` fest.</span><span class="sxs-lookup"><span data-stu-id="693be-314">Set the **Start Mode** to `AlwaysRunning`.</span></span> <span data-ttu-id="693be-315">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="693be-315">Select **OK**.</span></span>
  1. <span data-ttu-id="693be-316">Öffnen Sie den Knoten **Websites** im Bereich **Verbindungen**.</span><span class="sxs-lookup"><span data-stu-id="693be-316">Open the **Sites** node in the **Connections** panel.</span></span>
  1. <span data-ttu-id="693be-317">Klicken Sie mit der rechten Maustaste auf die App, und wählen Sie **Website verwalten** > **Erweiterte Einstellungen** aus.</span><span class="sxs-lookup"><span data-stu-id="693be-317">Right-click the app and select **Manage Website** > **Advanced Settings**.</span></span>
  1. <span data-ttu-id="693be-318">Die Standardeinstellung für **Vorabladen aktiviert** ist `False`.</span><span class="sxs-lookup"><span data-stu-id="693be-318">The default **Preload Enabled** setting is `False`.</span></span> <span data-ttu-id="693be-319">Legen Sie für **Vorabladen aktiviert** `True` fest.</span><span class="sxs-lookup"><span data-stu-id="693be-319">Set **Preload Enabled** to `True`.</span></span> <span data-ttu-id="693be-320">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="693be-320">Select **OK**.</span></span>

* <span data-ttu-id="693be-321">Fügen Sie mithilfe von `web.config` das `<applicationInitialization>`-Element, für das `doAppInitAfterRestart` auf `true` festgelegt ist, den `<system.webServer>`-Elementen in der `web.config`-Datei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="693be-321">Using `web.config`, add the `<applicationInitialization>` element with `doAppInitAfterRestart` set to `true` to the `<system.webServer>` elements in the app's `web.config` file:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a><span data-ttu-id="693be-322">Leerlauftimeout</span><span class="sxs-lookup"><span data-stu-id="693be-322">Idle Timeout</span></span>

<span data-ttu-id="693be-323">*Gilt nur für In-Process gehostete Apps.*</span><span class="sxs-lookup"><span data-stu-id="693be-323">*Only applies to apps hosted in-process.*</span></span>

<span data-ttu-id="693be-324">Um zu verhindern, dass die App in den Leerlauf wechselt, legen Sie das Leerlauftimeout des App-Pools mit IIS-Manager fest:</span><span class="sxs-lookup"><span data-stu-id="693be-324">To prevent the app from idling, set the app pool's idle timeout using IIS Manager:</span></span>

1. <span data-ttu-id="693be-325">Wählen Sie **Anwendungspools** im Bereich **Verbindungen** aus.</span><span class="sxs-lookup"><span data-stu-id="693be-325">Select **Application Pools** in the **Connections** panel.</span></span>
1. <span data-ttu-id="693be-326">Klicken Sie mit der rechten Maustaste im App-Pool der App in die Liste, und wählen Sie **Erweiterte Einstellungen** aus.</span><span class="sxs-lookup"><span data-stu-id="693be-326">Right-click the app's app pool in the list and select **Advanced Settings**.</span></span>
1. <span data-ttu-id="693be-327">Der Standardwert für **Leerlauftimeout (Minuten)** ist `20` Minuten.</span><span class="sxs-lookup"><span data-stu-id="693be-327">The default **Idle Time-out (minutes)** is `20` minutes.</span></span> <span data-ttu-id="693be-328">Legen Sie **Leerlauftimeout (Minuten)** auf `0` (null) fest.</span><span class="sxs-lookup"><span data-stu-id="693be-328">Set the **Idle Time-out (minutes)** to `0` (zero).</span></span> <span data-ttu-id="693be-329">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="693be-329">Select **OK**.</span></span>
1. <span data-ttu-id="693be-330">Recyceln Sie den Workerprozess.</span><span class="sxs-lookup"><span data-stu-id="693be-330">Recycle the worker process.</span></span>

<span data-ttu-id="693be-331">Um zu verhindern, dass in Apps, die [Out-of-Process](xref:host-and-deploy/iis/out-of-process-hosting) gehostet werden, ein Timeout auftritt, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="693be-331">To prevent apps hosted [out-of-process](xref:host-and-deploy/iis/out-of-process-hosting) from timing out, use either of the following approaches:</span></span>

* <span data-ttu-id="693be-332">Pingen Sie die App von einem externen Dienst aus, damit sie kontinuierlich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="693be-332">Ping the app from an external service in order to keep it running.</span></span>
* <span data-ttu-id="693be-333">Wenn die App nur Hintergrunddienste hostet, vermeiden Sie IIS-Hosting, und verwenden Sie einen [Windows-Dienst, um die ASP.NET Core-App zu hosten](xref:host-and-deploy/windows-service).</span><span class="sxs-lookup"><span data-stu-id="693be-333">If the app only hosts background services, avoid IIS hosting and use a [Windows Service to host the ASP.NET Core app](xref:host-and-deploy/windows-service).</span></span>

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a><span data-ttu-id="693be-334">Zusätzliche Ressourcen für das Anwendungsinitialisierungsmodul und das Leerlauftimeout</span><span class="sxs-lookup"><span data-stu-id="693be-334">Application Initialization Module and Idle Timeout additional resources</span></span>

* [<span data-ttu-id="693be-335">IIS 8.0 Anwendungsinitialisierung</span><span class="sxs-lookup"><span data-stu-id="693be-335">IIS 8.0 Application Initialization</span></span>](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* <span data-ttu-id="693be-336">[Anwendungsinitialisierung `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span><span class="sxs-lookup"><span data-stu-id="693be-336">[Application Initialization `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).</span></span>
* <span data-ttu-id="693be-337">[Verarbeiten von Modelleinstellungen für einen Anwendungspool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span><span class="sxs-lookup"><span data-stu-id="693be-337">[Process Model Settings for an Application Pool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).</span></span>

## <a name="module-schema-and-configuration-file-locations"></a><span data-ttu-id="693be-338">Dateispeicherorte für Modul, Schema und Konfiguration</span><span class="sxs-lookup"><span data-stu-id="693be-338">Module, schema, and configuration file locations</span></span>

### <a name="module"></a><span data-ttu-id="693be-339">Modul</span><span class="sxs-lookup"><span data-stu-id="693be-339">Module</span></span>

<span data-ttu-id="693be-340">**IIS (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="693be-340">**IIS (x86/amd64)** :</span></span>

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

<span data-ttu-id="693be-341">**IIS Express (x86/amd64):**</span><span class="sxs-lookup"><span data-stu-id="693be-341">**IIS Express (x86/amd64)** :</span></span>

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a><span data-ttu-id="693be-342">Schema</span><span class="sxs-lookup"><span data-stu-id="693be-342">Schema</span></span>

<span data-ttu-id="693be-343">**IIS**</span><span class="sxs-lookup"><span data-stu-id="693be-343">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

<span data-ttu-id="693be-344">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="693be-344">**IIS Express**</span></span>

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a><span data-ttu-id="693be-345">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="693be-345">Configuration</span></span>

<span data-ttu-id="693be-346">**IIS**</span><span class="sxs-lookup"><span data-stu-id="693be-346">**IIS**</span></span>

* `%windir%\System32\inetsrv\config\applicationHost.config`

<span data-ttu-id="693be-347">**IIS Express**</span><span class="sxs-lookup"><span data-stu-id="693be-347">**IIS Express**</span></span>

* <span data-ttu-id="693be-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span><span class="sxs-lookup"><span data-stu-id="693be-348">Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`</span></span>

* <span data-ttu-id="693be-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span><span class="sxs-lookup"><span data-stu-id="693be-349">*iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`</span></span>

<span data-ttu-id="693be-350">Den Speicherort dieser Dateien finden Sie, indem Sie `aspnetcore` in der Datei `applicationHost.config` suchen.</span><span class="sxs-lookup"><span data-stu-id="693be-350">The files can be found by searching for `aspnetcore` in the `applicationHost.config` file.</span></span>
