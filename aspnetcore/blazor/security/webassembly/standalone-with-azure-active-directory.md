---
title: Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie eine eigenständige Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory sichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 4e8c22c56b7023301499fd273a9194b8c7b58f3d
ms.sourcegitcommit: 45aa1c24c3fdeb939121e856282b00bdcf00ea55
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93343708"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="db03e-103">Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="db03e-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="db03e-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="db03e-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="db03e-105">In diesem Artikel wird beschrieben, wie Sie eine [eigenständige Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="db03e-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="db03e-106">Visual Studio konfiguriert Blazor WebAssembly-Apps, die in Visual Studio erstellt und für die Unterstützung von Konten in einem AAD-Organisationsverzeichnis konfiguriert wurden, bei der Projektgenerierung nicht ordnungsgemäß.</span><span class="sxs-lookup"><span data-stu-id="db03e-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="db03e-107">Dieses Problem wird in einem zukünftigen Release von Visual Studio behoben.</span><span class="sxs-lookup"><span data-stu-id="db03e-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="db03e-108">In diesem Artikel wird gezeigt, wie Sie die App mit dem Befehl `dotnet new` der .NET Core-CLI erstellen.</span><span class="sxs-lookup"><span data-stu-id="db03e-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="db03e-109">Wenn Sie die App lieber mit Visual Studio erstellen möchten, auch wenn die IDE noch nicht mit den neuesten Blazor-Vorlagen in ASP.NET Core 5.0 aktualisiert wurde, lesen Sie die einzelnen Abschnitte in diesem Artikel, und bestätigen oder aktualisieren Sie die Konfiguration der App, nachdem sie in Visual Studio erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="db03e-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest Blazor templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

<span data-ttu-id="db03e-110">Registrieren Sie eine AAD-App im Bereich **Azure Active Directory**>**App-Registrierungen** des Azure-Portals:</span><span class="sxs-lookup"><span data-stu-id="db03e-110">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="db03e-111">Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD** ).</span><span class="sxs-lookup"><span data-stu-id="db03e-111">Provide a **Name** for the app (for example, **Blazor Standalone AAD** ).</span></span>
1. <span data-ttu-id="db03e-112">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="db03e-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="db03e-113">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** auswählen.</span><span class="sxs-lookup"><span data-stu-id="db03e-113">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="db03e-114">Treffen Sie für **Umleitungs-URI** die Dropdownauswahl **Single-Page-Anwendung (SPA)** , und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="db03e-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="db03e-115">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="db03e-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="db03e-116">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="db03e-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="db03e-117">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="db03e-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="db03e-118">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="db03e-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="db03e-119">Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="db03e-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="db03e-120">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="db03e-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="db03e-121">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="db03e-121">Select **Register**.</span></span>

<span data-ttu-id="db03e-122">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="db03e-122">Record the following information:</span></span>

* <span data-ttu-id="db03e-123">Anwendungs-ID (Client-ID) (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="db03e-123">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="db03e-124">Verzeichnis-ID (Mandanten-ID) (z. B. `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="db03e-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="db03e-125">In **Authentifizierung** > **Plattformkonfigurationen** > **Single-Page-Anwendung (SPA)** :</span><span class="sxs-lookup"><span data-stu-id="db03e-125">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="db03e-126">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="db03e-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="db03e-127">Stellen Sie sicher, dass für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token** **nicht** aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="db03e-127">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="db03e-128">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="db03e-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="db03e-129">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="db03e-129">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="db03e-130">Registrieren Sie eine AAD-App im Bereich **Azure Active Directory**>**App-Registrierungen** des Azure-Portals:</span><span class="sxs-lookup"><span data-stu-id="db03e-130">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="db03e-131">Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD** ).</span><span class="sxs-lookup"><span data-stu-id="db03e-131">Provide a **Name** for the app (for example, **Blazor Standalone AAD** ).</span></span>
1. <span data-ttu-id="db03e-132">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="db03e-132">Choose a **Supported account types**.</span></span> <span data-ttu-id="db03e-133">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** auswählen.</span><span class="sxs-lookup"><span data-stu-id="db03e-133">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="db03e-134">Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web** , und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="db03e-134">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="db03e-135">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="db03e-135">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="db03e-136">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="db03e-136">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="db03e-137">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="db03e-137">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="db03e-138">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="db03e-138">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="db03e-139">Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="db03e-139">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="db03e-140">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="db03e-140">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="db03e-141">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="db03e-141">Select **Register**.</span></span>

<span data-ttu-id="db03e-142">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="db03e-142">Record the following information:</span></span>

* <span data-ttu-id="db03e-143">Anwendungs-ID (Client-ID) (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="db03e-143">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="db03e-144">Verzeichnis-ID (Mandanten-ID) (z. B. `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="db03e-144">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="db03e-145">In **Authentifizierung** > **Plattformkonfigurationen** > **Web** :</span><span class="sxs-lookup"><span data-stu-id="db03e-145">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="db03e-146">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="db03e-146">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="db03e-147">Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="db03e-147">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="db03e-148">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="db03e-148">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="db03e-149">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="db03e-149">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="db03e-150">Erstellen Sie die App in einem leeren Ordner.</span><span class="sxs-lookup"><span data-stu-id="db03e-150">Create the app in an empty folder.</span></span> <span data-ttu-id="db03e-151">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="db03e-151">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="db03e-152">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="db03e-152">Placeholder</span></span>   | <span data-ttu-id="db03e-153">Name im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="db03e-153">Azure portal name</span></span>       | <span data-ttu-id="db03e-154">Beispiel</span><span class="sxs-lookup"><span data-stu-id="db03e-154">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="db03e-155">Anwendungs-ID (Client)</span><span class="sxs-lookup"><span data-stu-id="db03e-155">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="db03e-156">Verzeichnis-ID (Mandant)</span><span class="sxs-lookup"><span data-stu-id="db03e-156">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="db03e-157">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="db03e-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="db03e-158">Im Azure-Portal ist die App-Plattformkonfiguration **Umleitungs-URI** für Apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="db03e-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="db03e-159">Wenn die App auf einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="db03e-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="db03e-160">Wenn der Port nicht zuvor mit dem bekannten Port der App konfiguriert wurde, kehren Sie zur App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.</span><span class="sxs-lookup"><span data-stu-id="db03e-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="db03e-161">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen durchführen können:</span><span class="sxs-lookup"><span data-stu-id="db03e-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="db03e-162">Anmelden bei der App mit einem AAD-Benutzerkonto</span><span class="sxs-lookup"><span data-stu-id="db03e-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="db03e-163">Anfordern von Zugriffstoken für Microsoft-APIs</span><span class="sxs-lookup"><span data-stu-id="db03e-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="db03e-164">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="db03e-164">For more information, see:</span></span>
  * [<span data-ttu-id="db03e-165">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="db03e-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="db03e-166">[Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="db03e-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="db03e-167">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="db03e-167">Authentication package</span></span>

<span data-ttu-id="db03e-168">Wenn eine App zum Verwenden von Geschäfts-, Schul- oder Unikonten (`SingleOrg`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="db03e-168">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="db03e-169">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="db03e-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="db03e-170">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="db03e-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="db03e-171">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="db03e-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="db03e-172">Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket transitiv hinzu.</span><span class="sxs-lookup"><span data-stu-id="db03e-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="db03e-173">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="db03e-173">Authentication service support</span></span>

<span data-ttu-id="db03e-174">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="db03e-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="db03e-175">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="db03e-175">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="db03e-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="db03e-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="db03e-177">Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="db03e-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="db03e-178">Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration beim Registrieren der App abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="db03e-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="db03e-179">Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="db03e-179">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="db03e-180">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="db03e-180">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="db03e-181">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="db03e-181">Access token scopes</span></span>

<span data-ttu-id="db03e-182">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch so, dass diese ein Zugriffstoken für eine sichere API anfordert.</span><span class="sxs-lookup"><span data-stu-id="db03e-182">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="db03e-183">Zum Bereitstellen eines Zugriffstokens als Teil des Anmeldeflows fügen Sie den Bereich den Standard-Zugriffstokenbereichen von <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> hinzu:</span><span class="sxs-lookup"><span data-stu-id="db03e-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="db03e-184">Geben Sie zusätzliche Bereiche mit `AdditionalScopesToConsent` an:</span><span class="sxs-lookup"><span data-stu-id="db03e-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="db03e-185">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios* :</span><span class="sxs-lookup"><span data-stu-id="db03e-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="db03e-186">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="db03e-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="db03e-187">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="db03e-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="db03e-188">Anmeldemodus</span><span class="sxs-lookup"><span data-stu-id="db03e-188">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="db03e-189">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="db03e-189">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="db03e-190">Indexseite</span><span class="sxs-lookup"><span data-stu-id="db03e-190">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="db03e-191">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="db03e-191">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="db03e-192">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="db03e-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="db03e-193">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="db03e-193">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="db03e-194">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="db03e-194">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="db03e-195">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="db03e-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="db03e-196">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="db03e-196">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="db03e-197">Dokumentation zu Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="db03e-197">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
