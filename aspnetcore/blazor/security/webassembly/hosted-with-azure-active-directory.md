---
title: Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie eine gehostete ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory sichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: e38838930dba70abfcfe4db9c204132e67866041
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280936"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="36da3-103">Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="36da3-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="36da3-104">In diesem Artikel wird beschrieben, wie Sie eine [gehostete Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="36da3-104">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="36da3-105">Für in Visual Studio erstellte Blazor WebAssembly-Apps, die zur Unterstützung von Konten in einem AAD-Organisationsverzeichnis konfiguriert sind, konfiguriert Visual Studio die Projekte der Projektmappe oder die Registrierungen der Azure-Portal-App bei der Projektgenerierung derzeit nicht richtig.</span><span class="sxs-lookup"><span data-stu-id="36da3-105">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure the solution's projects or the Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="36da3-106">Dieses Problem wird in einem zukünftigen Release von Visual Studio behoben.</span><span class="sxs-lookup"><span data-stu-id="36da3-106">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="36da3-107">Dieser Artikel zeigt, wie die Projektmappe und die Registrierungen für das Azure-App-Portal mit dem .NET-Befehl `dotnet new` der CLI und durch manuelles Erstellen der App-Registrierungen im Azure-Portal erstellt werden können.</span><span class="sxs-lookup"><span data-stu-id="36da3-107">This article shows how to create the solution and Azure app portal registrations with the .NET CLI `dotnet new` command and by manually creating the app registrations in the Azure portal.</span></span>
>
> <span data-ttu-id="36da3-108">Wenn Sie es vorziehen, die Projektmappe und die Azure-App-Registrierungen mit Visual Studio zu erstellen, bevor die IDE aktualisiert wird, lesen Sie **_jeden Abschnitt dieses Artikels_**, und bestätigen oder aktualisieren Sie die Konfigurationen der Apps und die Registrierungen der Apps, nachdem Visual Studio die Projektmappe erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="36da3-108">If you prefer to create the solution and Azure app registrations with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the apps' configurations and the apps' registrations after Visual Studio creates the solution.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="36da3-109">Registrieren von Apps in AAD und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="36da3-109">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="36da3-110">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="36da3-110">Create a tenant</span></span>

<span data-ttu-id="36da3-111">Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten eines Mandanten](/azure/active-directory/develop/quickstart-create-new-tenant), um einen Mandanten in AAD zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="36da3-111">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="36da3-112">Registrieren einer Server-API-App</span><span class="sxs-lookup"><span data-stu-id="36da3-112">Register a server API app</span></span>

<span data-ttu-id="36da3-113">Registrieren einer AAD-App für die *Server-API-App*:</span><span class="sxs-lookup"><span data-stu-id="36da3-113">Register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="36da3-114">Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.</span><span class="sxs-lookup"><span data-stu-id="36da3-114">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="36da3-115">Geben Sie einen **Namen** für die App an (z. B. **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="36da3-115">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="36da3-116">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="36da3-116">Choose a **Supported account types**.</span></span> <span data-ttu-id="36da3-117">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.</span><span class="sxs-lookup"><span data-stu-id="36da3-117">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="36da3-118">Die *Server-API-App* erfordert in diesem Szenario keinen **Umleitungs-URI**. Belassen Sie daher die Dropdownauswahl bei **Web**, und geben Sie keinen Umleitungs-URI ein.</span><span class="sxs-lookup"><span data-stu-id="36da3-118">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="36da3-119">Deaktivieren Sie das Kontrollkästchen bei **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="36da3-119">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="36da3-120">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="36da3-120">Select **Register**.</span></span>

<span data-ttu-id="36da3-121">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="36da3-121">Record the following information:</span></span>

* <span data-ttu-id="36da3-122">Anwendungs-ID (Client-ID) der *Server-API-App* (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="36da3-122">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="36da3-123">Verzeichnis-ID (Mandanten-ID) (z. B. `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="36da3-123">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="36da3-124">Primäre, Herausgeber- oder Mandantendomäne für AAD (z. B. `contoso.onmicrosoft.com`): Die Domäne ist als **Herausgeberdomäne** auf dem Blatt **Branding** des Azure-Portals für die registrierte App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="36da3-124">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="36da3-125">Entfernen Sie unter **API-Berechtigungen** die Berechtigung **Microsoft Graph** > **User.Read**, da die App keine Anmeldung oder Zugriff auf Benutzerprofile benötigt.</span><span class="sxs-lookup"><span data-stu-id="36da3-125">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="36da3-126">Gehen Sie unter **Eine API verfügbar machen** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="36da3-126">In **Expose an API**:</span></span>

1. <span data-ttu-id="36da3-127">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="36da3-127">Select **Add a scope**.</span></span>
1. <span data-ttu-id="36da3-128">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="36da3-128">Select **Save and continue**.</span></span>
1. <span data-ttu-id="36da3-129">Geben Sie einen **Bereichsnamen** an (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="36da3-129">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="36da3-130">Geben Sie einen **Anzeigenamen der Administratorzustimmung** an (z. B. `Access API`).</span><span class="sxs-lookup"><span data-stu-id="36da3-130">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="36da3-131">Geben Sie eine **Beschreibung der Administratorzustimmung** an (z. B. `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="36da3-131">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="36da3-132">Vergewissern Sie sich, dass **Zustand** auf **Aktiviert** gesetzt ist.</span><span class="sxs-lookup"><span data-stu-id="36da3-132">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="36da3-133">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="36da3-133">Select **Add scope**.</span></span>

<span data-ttu-id="36da3-134">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="36da3-134">Record the following information:</span></span>

* <span data-ttu-id="36da3-135">App-ID-URI (z. B. `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="36da3-135">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="36da3-136">Bereichsname (beispielsweise `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="36da3-136">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="36da3-137">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="36da3-137">Register a client app</span></span>

<span data-ttu-id="36da3-138">Registrieren einer AAD-App für die *Client-App*:</span><span class="sxs-lookup"><span data-stu-id="36da3-138">Register an AAD app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="36da3-139">Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="36da3-139">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="36da3-140">Geben Sie einen **Namen** für die App an (z. B. **Blazor Client AAD**).</span><span class="sxs-lookup"><span data-stu-id="36da3-140">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="36da3-141">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="36da3-141">Choose a **Supported account types**.</span></span> <span data-ttu-id="36da3-142">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.</span><span class="sxs-lookup"><span data-stu-id="36da3-142">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="36da3-143">Treffen Sie für **Umleitungs-URI** die Dropdownauswahl **Single-Page-Anwendung (SPA)** , und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="36da3-143">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="36da3-144">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="36da3-144">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="36da3-145">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="36da3-145">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="36da3-146">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der *`Server`* -App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="36da3-146">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="36da3-147">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="36da3-147">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="36da3-148">Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="36da3-148">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="36da3-149">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="36da3-149">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="36da3-150">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="36da3-150">Select **Register**.</span></span>

<span data-ttu-id="36da3-151">Notieren Sie sich die Anwendungs-ID (Client-ID) der *`Client`* -App (z. B. `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="36da3-151">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="36da3-152">In **Authentifizierung** > **Plattformkonfigurationen** > **Single-Page-Anwendung (SPA)** :</span><span class="sxs-lookup"><span data-stu-id="36da3-152">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="36da3-153">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="36da3-153">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="36da3-154">Stellen Sie sicher, dass für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token** **nicht** aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="36da3-154">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="36da3-155">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="36da3-155">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="36da3-156">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="36da3-156">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="36da3-157">Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="36da3-157">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="36da3-158">Geben Sie einen **Namen** für die App an (z. B. **Blazor Client AAD**).</span><span class="sxs-lookup"><span data-stu-id="36da3-158">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="36da3-159">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="36da3-159">Choose a **Supported account types**.</span></span> <span data-ttu-id="36da3-160">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.</span><span class="sxs-lookup"><span data-stu-id="36da3-160">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="36da3-161">Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="36da3-161">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="36da3-162">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="36da3-162">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="36da3-163">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="36da3-163">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="36da3-164">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der *`Server`* -App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="36da3-164">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="36da3-165">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="36da3-165">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="36da3-166">Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="36da3-166">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="36da3-167">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="36da3-167">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="36da3-168">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="36da3-168">Select **Register**.</span></span>

<span data-ttu-id="36da3-169">Notieren Sie sich die Anwendungs-ID (Client-ID) der *`Client`* -App (z. B. `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="36da3-169">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="36da3-170">In **Authentifizierung** > **Plattformkonfigurationen** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="36da3-170">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="36da3-171">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="36da3-171">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="36da3-172">Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="36da3-172">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="36da3-173">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="36da3-173">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="36da3-174">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="36da3-174">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="36da3-175">Gehen Sie unter **API-Berechtigungen** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="36da3-175">In **API permissions**:</span></span>

1. <span data-ttu-id="36da3-176">Sorgen Sie dafür, dass die App über die Berechtigung **Microsoft Graph** > **User.Read** verfügt.</span><span class="sxs-lookup"><span data-stu-id="36da3-176">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="36da3-177">Klicken Sie auf **Berechtigung hinzufügen** und dann auf **Meine APIs**.</span><span class="sxs-lookup"><span data-stu-id="36da3-177">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="36da3-178">Wählen Sie die *Server-API-App* aus der Spalte **Name** aus (z. B. **Blazor Server AAD**).</span><span class="sxs-lookup"><span data-stu-id="36da3-178">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="36da3-179">Öffnen Sie die **API**-Liste.</span><span class="sxs-lookup"><span data-stu-id="36da3-179">Open the **API** list.</span></span>
1. <span data-ttu-id="36da3-180">Ermöglichen Sie den Zugriff auf die API (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="36da3-180">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="36da3-181">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="36da3-181">Select **Add permissions**.</span></span>
1. <span data-ttu-id="36da3-182">Klicken Sie auf die Schaltfläche **Administratoreinwilligung für {MANDANTENNAME} erteilen**.</span><span class="sxs-lookup"><span data-stu-id="36da3-182">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="36da3-183">Klicken Sie auf **Ja**, um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="36da3-183">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="36da3-184">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="36da3-184">Create the app</span></span>

<span data-ttu-id="36da3-185">Verwenden Sie einen leeren Ordner, ersetzen Sie im folgenden Befehl die Platzhalter durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="36da3-185">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="36da3-186">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="36da3-186">Placeholder</span></span>                  | <span data-ttu-id="36da3-187">Name im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="36da3-187">Azure portal name</span></span>                                     | <span data-ttu-id="36da3-188">Beispiel</span><span class="sxs-lookup"><span data-stu-id="36da3-188">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="36da3-189">Anwendungs-ID (Client-ID) für die *`Client`* -App</span><span class="sxs-lookup"><span data-stu-id="36da3-189">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="36da3-190">Bereichsname</span><span class="sxs-lookup"><span data-stu-id="36da3-190">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="36da3-191">Anwendungs-ID (Client-ID) für die *Server-API-App*</span><span class="sxs-lookup"><span data-stu-id="36da3-191">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="36da3-192">Anwendungs-ID-URI&dagger;</span><span class="sxs-lookup"><span data-stu-id="36da3-192">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="36da3-193">Primäre, Herausgeber- oder Mandantendomäne</span><span class="sxs-lookup"><span data-stu-id="36da3-193">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="36da3-194">Verzeichnis-ID (Mandant)</span><span class="sxs-lookup"><span data-stu-id="36da3-194">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="36da3-195">&dagger;Die Blazor WebAssembly-Vorlage fügt dem App-ID-URI-Argument, das im Befehl `dotnet new` übergeben wird, automatisch ein `api://`-Schema hinzu.</span><span class="sxs-lookup"><span data-stu-id="36da3-195">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="36da3-196">Wenn Sie den App-ID-URI für den Platzhalter `{SERVER API APP ID URI}` bereitstellen und das Schema `api://` lautet, entfernen Sie das Schema (`api://`) aus dem Argument. Dies wird anhand des Beispielwerts in der obigen Tabelle gezeigt.</span><span class="sxs-lookup"><span data-stu-id="36da3-196">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="36da3-197">Wenn der App-ID-URI ein benutzerdefinierter Wert ist oder ein anderes Schema aufweist (z. B. `https://` für eine nicht vertrauenswürdige Herausgeberdomäne wie `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), müssen Sie den Standard-URI für den Geltungsbereich manuell aktualisieren und das Schema `api://` entfernen, nachdem die *`Client`* -App von der Vorlage erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="36da3-197">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="36da3-198">Weitere Informationen finden Sie im Hinweis im Abschnitt [Geltungsbereiche von Zugriffstoken](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="36da3-198">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="36da3-199">Die Blazor WebAssembly-Vorlage wird möglicherweise in einem zukünftigen Release von ASP.NET Core geändert werden, um diese Szenarios zu beheben.</span><span class="sxs-lookup"><span data-stu-id="36da3-199">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="36da3-200">Weitere Informationen finden Sie unter [Doppeltes Schema für den App-ID-URI in Blazor WASM-Vorlage (gehostet, Einzelorganisation) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="36da3-200">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="36da3-201">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="36da3-201">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="36da3-202">Eine Konfigurationsänderung ist möglicherweise erforderlich, wenn Sie einen Azure-Mandanten mit einer nicht überprüften Herausgeberdomäne verwenden, was im Abschnitt [App-Einstellungen](#app-settings) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="36da3-202">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="36da3-203">Eine Konfigurationsänderung ist möglicherweise erforderlich, wenn Sie einen Azure-Mandanten mit einer nicht überprüften Herausgeberdomäne verwenden, was im Abschnitt [Zugriffstokenbereiche](#access-token-scopes) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="36da3-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="36da3-204">Im Azure-Portal ist der **Umleitungs-URI** für die Plattformkonfiguration der *`Client`* -App für Apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="36da3-204">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="36da3-205">Wenn die *`Client`* -App an einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der *Server-API-App* im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="36da3-205">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="36da3-206">Wenn der Port nicht zuvor mit dem bekannten Port der *`Client`* -App konfiguriert wurde, kehren Sie zur *`Client`* -App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.</span><span class="sxs-lookup"><span data-stu-id="36da3-206">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="36da3-207">*`Server`* -App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="36da3-207">*`Server`* app configuration</span></span>

<span data-ttu-id="36da3-208">*Dieser Abschnitt bezieht sich auf die **`Server`** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="36da3-208">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="36da3-209">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="36da3-209">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="36da3-210">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs mit der Microsoft Identity Platform wird von folgenden Paketen bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="36da3-210">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="36da3-211">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der App übereinstimmt, im **Versionsverlauf** des Pakets auf NuGet.org ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="36da3-211">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="36da3-212">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs wird vom Paket [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="36da3-212">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="36da3-213">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="36da3-213">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="36da3-214">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="36da3-214">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="36da3-215">Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="36da3-215">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="36da3-216">Mit der <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A>-Methode werden Dienste konfiguriert, um die Web-API mit Microsoft Identity Platform v 2.0 zu schützen.</span><span class="sxs-lookup"><span data-stu-id="36da3-216">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="36da3-217">Diese Methode erwartet einen `AzureAd`-Abschnitt in der App-Konfiguration mit den erforderlichen Einstellungen zum Initialisieren der Authentifizierungsoptionen.</span><span class="sxs-lookup"><span data-stu-id="36da3-217">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="36da3-218">Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="36da3-218">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="36da3-219">Die <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>-Methode richtet die spezifischen Parameter im JWT-Bearerhandler ein, die zum Überprüfen der von Azure Active Directory ausgegebenen Token erforderlich sind:</span><span class="sxs-lookup"><span data-stu-id="36da3-219">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="36da3-220"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> stellen Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="36da3-220"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="36da3-221">Die App versucht, Token auf eingehende Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="36da3-221">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="36da3-222">Jede Anforderung, die versucht, ohne die richtigen Anmeldeinformationen auf eine geschützte Ressource zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="36da3-222">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="36da3-223">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="36da3-223">User.Identity.Name</span></span>

<span data-ttu-id="36da3-224">Standardmäßig füllt die *`Server`* -App-API `User.Identity.Name` mit dem Wert aus dem `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`-Anspruchstyp (z. B. `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`) auf.</span><span class="sxs-lookup"><span data-stu-id="36da3-224">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="36da3-225">So konfigurieren Sie die App, um den Wert vom `name`-Anspruchstyp zu empfangen:</span><span class="sxs-lookup"><span data-stu-id="36da3-225">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="36da3-226">Fügen Sie `Startup.cs` einen Namespace für <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="36da3-226">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="36da3-227">Konfigurieren Sie den <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36da3-227">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="36da3-228">Konfigurieren Sie den <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="36da3-228">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="36da3-229">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="36da3-229">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="36da3-230">Die Datei `appsettings.json` enthält die Optionen zum Konfigurieren des JWT-Bearerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="36da3-230">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="36da3-231">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="36da3-231">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/blazor/includes/security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="36da3-232">Die Datei `appsettings.json` enthält die Optionen zum Konfigurieren des JWT-Bearerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="36da3-232">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="36da3-233">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="36da3-233">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="36da3-234">WeatherForecast-Controller</span><span class="sxs-lookup"><span data-stu-id="36da3-234">WeatherForecast controller</span></span>

<span data-ttu-id="36da3-235">Der WeatherForecast-Controller (*Controllers/WeatherForecastController.cs*) macht eine geschützte API mit auf den Controller angewendetem [`[Authorize]`-Attribut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="36da3-235">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) applied to the controller.</span></span> <span data-ttu-id="36da3-236">Es ist **wichtig**, Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="36da3-236">It's **important** to understand that:</span></span>

* <span data-ttu-id="36da3-237">Das [`[Authorize]`-Attribut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="36da3-237">The [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="36da3-238">Das in der Blazor WebAssembly-App verwendete [`[Authorize]`-Attribut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) dient nur als Hinweis für die App, dass der Benutzer autorisiert sein sollte, damit die App richtig funktioniert.</span><span class="sxs-lookup"><span data-stu-id="36da3-238">The [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="36da3-239">*`Client`* -App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="36da3-239">*`Client`* app configuration</span></span>

<span data-ttu-id="36da3-240">*Dieser Abschnitt bezieht sich auf die **`Client`** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="36da3-240">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="36da3-241">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="36da3-241">Authentication package</span></span>

<span data-ttu-id="36da3-242">Wenn eine App zum Verwenden von Geschäfts-, Schul- oder Unikonten (`SingleOrg`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="36da3-242">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="36da3-243">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="36da3-243">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="36da3-244">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="36da3-244">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="36da3-245">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="36da3-245">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="36da3-246">Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket transitiv hinzu.</span><span class="sxs-lookup"><span data-stu-id="36da3-246">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="36da3-247">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="36da3-247">Authentication service support</span></span>

<span data-ttu-id="36da3-248">Es wird Unterstützung für <xref:System.Net.Http.HttpClient>-Instanzen hinzugefügt, die bei Anforderungen an das Serverprojekt Zugriffstoken einschließen.</span><span class="sxs-lookup"><span data-stu-id="36da3-248">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="36da3-249">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="36da3-249">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="36da3-250">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="36da3-250">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="36da3-251">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="36da3-251">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="36da3-252">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="36da3-252">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="36da3-253">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="36da3-253">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="36da3-254">Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="36da3-254">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="36da3-255">Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration im Azure-Portal beim Registrieren der App abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="36da3-255">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="36da3-256">Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="36da3-256">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="36da3-257">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="36da3-257">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="36da3-258">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="36da3-258">Access token scopes</span></span>

<span data-ttu-id="36da3-259">Zu den Standard-Zugriffstokenbereichen zählen die Zugriffstokenbereiche, auf die Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="36da3-259">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="36da3-260">standardmäßig in der Anmeldeanforderung enthalten</span><span class="sxs-lookup"><span data-stu-id="36da3-260">Included by default in the sign in request.</span></span>
* <span data-ttu-id="36da3-261">zum Bereitstellen eines Zugriffstokens direkt nach der Authentifizierung verwendet</span><span class="sxs-lookup"><span data-stu-id="36da3-261">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="36da3-262">Alle Bereiche müssen gemäß den Azure Active Directory-Regeln zur selben App gehören.</span><span class="sxs-lookup"><span data-stu-id="36da3-262">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="36da3-263">Zusätzliche Bereiche können nach Bedarf für zusätzliche API-Apps hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="36da3-263">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="36da3-264">Die Blazor WebAssembly-Vorlage fügt dem App-ID-URI-Argument, das im Befehl `dotnet new` übergeben wird, automatisch ein `api://`-Schema hinzu.</span><span class="sxs-lookup"><span data-stu-id="36da3-264">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="36da3-265">Wenn eine App aus der Blazor-Projektvorlage generiert wird, überprüfen Sie, ob der Wert des Bereichs für das Standardzugriffstoken entweder den richtigen URI-Wert für die App-ID verwendet, den Sie im Azure-Portal bereitgestellt haben, oder einen Wert mit **einem** der folgenden Formate:</span><span class="sxs-lookup"><span data-stu-id="36da3-265">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="36da3-266">Wenn die Domäne des Herausgebers des Verzeichnisses als **vertrauenswürdig** gilt, weist der Bereich des Standardzugriffstoken in der Regel einen Wert ähnlich des folgenden Beispiels auf. Dabei ist `API.Access` der Name des Standardbereichs:</span><span class="sxs-lookup"><span data-stu-id="36da3-266">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="36da3-267">Überprüfen Sie, ob der Wert ein doppeltes Schema aufweist (`api://api://...`).</span><span class="sxs-lookup"><span data-stu-id="36da3-267">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="36da3-268">Wenn ein doppeltes Schema vorhanden ist, entfernen Sie das erste `api://`-Schema aus dem Wert.</span><span class="sxs-lookup"><span data-stu-id="36da3-268">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="36da3-269">Wenn die Domäne des Herausgebers des Verzeichnisses als **nicht vertrauenswürdig** gilt, weist der Bereich des Standardzugriffstoken in der Regel einen Wert ähnlich des folgenden Beispiels auf. Dabei ist `API.Access` der Name des Standardbereichs:</span><span class="sxs-lookup"><span data-stu-id="36da3-269">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="36da3-270">Überprüfen Sie, ob der Wert ein zusätzliches `api://`-Schema aufweist (`api://https://contoso.onmicrosoft.com/...`).</span><span class="sxs-lookup"><span data-stu-id="36da3-270">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="36da3-271">Wenn ein zusätzliches `api://`-Schema vorhanden ist, entfernen Sie das erste `api://`-Schema aus dem Wert.</span><span class="sxs-lookup"><span data-stu-id="36da3-271">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="36da3-272">Die Blazor WebAssembly-Vorlage wird möglicherweise in einem zukünftigen Release von ASP.NET Core geändert werden, um diese Szenarios zu beheben.</span><span class="sxs-lookup"><span data-stu-id="36da3-272">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="36da3-273">Weitere Informationen finden Sie unter [Doppeltes Schema für den App-ID-URI in Blazor WASM-Vorlage (gehostet, Einzelorganisation) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="36da3-273">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="36da3-274">Geben Sie zusätzliche Bereiche mit `AdditionalScopesToConsent` an:</span><span class="sxs-lookup"><span data-stu-id="36da3-274">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="36da3-275">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:</span><span class="sxs-lookup"><span data-stu-id="36da3-275">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="36da3-276">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="36da3-276">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="36da3-277">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="36da3-277">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="36da3-278">Anmeldemodus</span><span class="sxs-lookup"><span data-stu-id="36da3-278">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="36da3-279">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="36da3-279">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="36da3-280">Indexseite</span><span class="sxs-lookup"><span data-stu-id="36da3-280">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="36da3-281">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="36da3-281">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="36da3-282">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="36da3-282">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="36da3-283">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="36da3-283">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="36da3-284">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="36da3-284">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="36da3-285">FetchData-Komponente</span><span class="sxs-lookup"><span data-stu-id="36da3-285">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="36da3-286">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="36da3-286">Run the app</span></span>

<span data-ttu-id="36da3-287">Führen Sie die App aus dem Serverprojekt aus.</span><span class="sxs-lookup"><span data-stu-id="36da3-287">Run the app from the Server project.</span></span> <span data-ttu-id="36da3-288">Wenn Sie Visual Studio verwenden, haben Sie folgende beiden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="36da3-288">When using Visual Studio, either:</span></span>

* <span data-ttu-id="36da3-289">Wählen Sie in der Dropdownliste **Startprojekte** in der Symbolleiste die *Server-API-App* aus, und klicken Sie auf **Ausführen**.</span><span class="sxs-lookup"><span data-stu-id="36da3-289">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="36da3-290">Wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und klicken Sie in der Symbolleiste auf **Ausführen**, oder starten Sie die App über das Menü **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="36da3-290">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="36da3-291">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="36da3-291">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="36da3-292">Erstellen einer benutzerdefinierten Version der Authentication.MSAL-JavaScript-Bibliothek</span><span class="sxs-lookup"><span data-stu-id="36da3-292">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="36da3-293">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="36da3-293">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="36da3-294">Dokumentation zu Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="36da3-294">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
* [<span data-ttu-id="36da3-295">Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="36da3-295">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app)
