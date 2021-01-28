---
title: Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory B2C
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie eine gehostete ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory B2C sichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 1c87330dec069e05f274206d2d35f50f489f9623
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710619"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="397c7-103">Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="397c7-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="397c7-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="397c7-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="397c7-105">In diesem Artikel wird beschrieben, wie Sie eine [gehostete Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory B2C (AAD)](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="397c7-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="397c7-106">Registrieren von Apps in AAD B2C und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="397c7-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="397c7-107">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="397c7-107">Create a tenant</span></span>

<span data-ttu-id="397c7-108">Befolgen Sie die Anweisungen unter [Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten](/azure/active-directory-b2c/tutorial-create-tenant), um einen AAD B2C-Mandanten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="397c7-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span> <span data-ttu-id="397c7-109">Kehren Sie direkt nach dem Erstellen oder Identifizieren eines zu verwendenden Mandanten zu diesem Artikel zurück.</span><span class="sxs-lookup"><span data-stu-id="397c7-109">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="397c7-110">Notieren Sie sich die AAD B2C-Instanz (z. B. `https://contoso.b2clogin.com/`, wobei der nachgestellte Schrägstrich enthalten ist).</span><span class="sxs-lookup"><span data-stu-id="397c7-110">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="397c7-111">Die Instanz ist das Schema und der Host einer Azure-B2B-App-Registrierung, die Sie im Fenster **Endpunkte** auf der Seite **App-Registrierungen** im Azure-Portal finden.</span><span class="sxs-lookup"><span data-stu-id="397c7-111">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="397c7-112">Registrieren einer Server-API-App</span><span class="sxs-lookup"><span data-stu-id="397c7-112">Register a server API app</span></span>

<span data-ttu-id="397c7-113">Registrieren einer AAD B2C-App für die *Server-API-App*:</span><span class="sxs-lookup"><span data-stu-id="397c7-113">Register an AAD B2C app for the *Server API app*:</span></span>

1. <span data-ttu-id="397c7-114">Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.</span><span class="sxs-lookup"><span data-stu-id="397c7-114">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="397c7-115">Geben Sie einen **Namen** für die App an (z. B. **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="397c7-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="397c7-116">Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)**</span><span class="sxs-lookup"><span data-stu-id="397c7-116">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="397c7-117">Die *Server-API-App* erfordert in diesem Szenario keinen **Umleitungs-URI**. Belassen Sie daher die Dropdownauswahl bei **Web**, und geben Sie keinen Umleitungs-URI ein.</span><span class="sxs-lookup"><span data-stu-id="397c7-117">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="397c7-118">Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="397c7-118">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="397c7-119">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="397c7-119">Select **Register**.</span></span>

<span data-ttu-id="397c7-120">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="397c7-120">Record the following information:</span></span>

* <span data-ttu-id="397c7-121">Anwendungs-ID (Client-ID) der *Server-API-App* (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="397c7-121">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="397c7-122">Primäre, Herausgeber- oder Mandantendomäne für AAD (z. B. `contoso.onmicrosoft.com`): Die Domäne ist als **Herausgeberdomäne** auf dem Blatt **Branding** des Azure-Portals für die registrierte App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="397c7-122">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="397c7-123">Gehen Sie unter **Eine API verfügbar machen** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="397c7-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="397c7-124">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="397c7-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="397c7-125">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="397c7-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="397c7-126">Geben Sie einen **Bereichsnamen** an (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="397c7-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="397c7-127">Geben Sie einen **Anzeigenamen der Administratorzustimmung** an (z. B. `Access API`).</span><span class="sxs-lookup"><span data-stu-id="397c7-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="397c7-128">Geben Sie eine **Beschreibung der Administratorzustimmung** an (z. B. `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="397c7-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="397c7-129">Vergewissern Sie sich, dass **Zustand** auf **Aktiviert** gesetzt ist.</span><span class="sxs-lookup"><span data-stu-id="397c7-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="397c7-130">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="397c7-130">Select **Add scope**.</span></span>

<span data-ttu-id="397c7-131">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="397c7-131">Record the following information:</span></span>

* <span data-ttu-id="397c7-132">App-ID-URI (z. B. `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="397c7-132">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="397c7-133">Bereichsname (beispielsweise `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="397c7-133">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="397c7-134">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="397c7-134">Register a client app</span></span>

<span data-ttu-id="397c7-135">Registrieren einer AAD B2C-App für die *Client-App*:</span><span class="sxs-lookup"><span data-stu-id="397c7-135">Register an AAD B2C app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="397c7-136">Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="397c7-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="397c7-137">Geben Sie einen **Namen** für die App an (z. B. **Blazor Client AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="397c7-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="397c7-138">Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)**</span><span class="sxs-lookup"><span data-stu-id="397c7-138">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="397c7-139">Treffen Sie für **Umleitungs-URI** die Dropdownauswahl **Single-Page-Anwendung (SPA)** , und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="397c7-139">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="397c7-140">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="397c7-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="397c7-141">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="397c7-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="397c7-142">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der *`Server`* -App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="397c7-142">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="397c7-143">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="397c7-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="397c7-144">Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="397c7-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="397c7-145">Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="397c7-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="397c7-146">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="397c7-146">Select **Register**.</span></span>

1. <span data-ttu-id="397c7-147">Notieren Sie sich die Anwendungs-ID (Client-ID) (z. B. `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="397c7-147">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="397c7-148">In **Authentifizierung** > **Plattformkonfigurationen** > **Single-Page-Anwendung (SPA)** :</span><span class="sxs-lookup"><span data-stu-id="397c7-148">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="397c7-149">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="397c7-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="397c7-150">Stellen Sie sicher, dass für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token** **nicht** aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="397c7-150">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="397c7-151">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="397c7-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="397c7-152">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="397c7-152">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="397c7-153">Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="397c7-153">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="397c7-154">Geben Sie einen **Namen** für die App an (z. B. **Blazor Client AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="397c7-154">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="397c7-155">Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)**</span><span class="sxs-lookup"><span data-stu-id="397c7-155">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="397c7-156">Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="397c7-156">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="397c7-157">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="397c7-157">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="397c7-158">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="397c7-158">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="397c7-159">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der *`Server`* -App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="397c7-159">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="397c7-160">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="397c7-160">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="397c7-161">Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="397c7-161">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="397c7-162">Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="397c7-162">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="397c7-163">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="397c7-163">Select **Register**.</span></span>

<span data-ttu-id="397c7-164">Notieren Sie sich die Anwendungs-ID (Client-ID) (z. B. `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="397c7-164">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="397c7-165">In **Authentifizierung** > **Plattformkonfigurationen** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="397c7-165">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="397c7-166">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="397c7-166">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="397c7-167">Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="397c7-167">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="397c7-168">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="397c7-168">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="397c7-169">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="397c7-169">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="397c7-170">Gehen Sie unter **API-Berechtigungen** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="397c7-170">In **API permissions**:</span></span>

1. <span data-ttu-id="397c7-171">Klicken Sie auf **Berechtigung hinzufügen** und dann auf **Meine APIs**.</span><span class="sxs-lookup"><span data-stu-id="397c7-171">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="397c7-172">Wählen Sie die *Server-API-App* aus der Spalte **Name** aus (z. B. **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="397c7-172">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="397c7-173">Öffnen Sie die **API**-Liste.</span><span class="sxs-lookup"><span data-stu-id="397c7-173">Open the **API** list.</span></span>
1. <span data-ttu-id="397c7-174">Ermöglichen Sie den Zugriff auf die API (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="397c7-174">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="397c7-175">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="397c7-175">Select **Add permissions**.</span></span>
1. <span data-ttu-id="397c7-176">Klicken Sie auf die Schaltfläche **Administratoreinwilligung für {MANDANTENNAME} erteilen**.</span><span class="sxs-lookup"><span data-stu-id="397c7-176">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="397c7-177">Klicken Sie auf **Ja**, um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="397c7-177">Select **Yes** to confirm.</span></span>

<span data-ttu-id="397c7-178">Gehen Sie unter **Startseite** > **Azure AD B2C** > **Benutzerflows** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="397c7-178">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="397c7-179">Erstellen Sie einen Benutzerflow für die Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="397c7-179">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="397c7-180">Wählen Sie mindestens das Benutzerattribut **Anwendungsansprüche** > **Anzeigename** aus, um `context.User.Identity.Name` in der `LoginDisplay`-Komponente (`Shared/LoginDisplay.razor`) auszufüllen.</span><span class="sxs-lookup"><span data-stu-id="397c7-180">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="397c7-181">Notieren Sie sich den Namen des für die App erstellten Benutzerflows für die Registrierung und Anmeldung (z. B. `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="397c7-181">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="397c7-182">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="397c7-182">Create the app</span></span>

<span data-ttu-id="397c7-183">Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="397c7-183">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="397c7-184">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="397c7-184">Placeholder</span></span>                   | <span data-ttu-id="397c7-185">Name im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="397c7-185">Azure portal name</span></span>                                     | <span data-ttu-id="397c7-186">Beispiel</span><span class="sxs-lookup"><span data-stu-id="397c7-186">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="397c7-187">Instanz</span><span class="sxs-lookup"><span data-stu-id="397c7-187">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="397c7-188">Anwendungs-ID (Client-ID) für die *`Client`* -App</span><span class="sxs-lookup"><span data-stu-id="397c7-188">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="397c7-189">Bereichsname</span><span class="sxs-lookup"><span data-stu-id="397c7-189">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="397c7-190">Anwendungs-ID (Client-ID) für die *Server-API-App*</span><span class="sxs-lookup"><span data-stu-id="397c7-190">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="397c7-191">Anwendungs-ID-URI&dagger;</span><span class="sxs-lookup"><span data-stu-id="397c7-191">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="397c7-192">Benutzerflow für die Registrierung oder Anmeldung</span><span class="sxs-lookup"><span data-stu-id="397c7-192">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="397c7-193">Primäre, Herausgeber- oder Mandantendomäne</span><span class="sxs-lookup"><span data-stu-id="397c7-193">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="397c7-194">&dagger;Die Blazor WebAssembly-Vorlage fügt dem App-ID-URI-Argument, das im Befehl `dotnet new` übergeben wird, automatisch ein `api://`-Schema hinzu.</span><span class="sxs-lookup"><span data-stu-id="397c7-194">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="397c7-195">Wenn Sie den App-ID-URI für den Platzhalter `{SERVER API APP ID URI}` bereitstellen und das Schema `api://` lautet, entfernen Sie das Schema (`api://`) aus dem Argument. Dies wird anhand des Beispielwerts in der obigen Tabelle gezeigt.</span><span class="sxs-lookup"><span data-stu-id="397c7-195">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="397c7-196">Wenn der App-ID-URI ein benutzerdefinierter Wert ist oder ein anderes Schema aufweist (z. B. `https://` für eine nicht vertrauenswürdige Herausgeberdomäne wie `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), müssen Sie den Standard-URI für den Geltungsbereich manuell aktualisieren und das Schema `api://` entfernen, nachdem die *`Client`* -App von der Vorlage erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="397c7-196">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="397c7-197">Weitere Informationen finden Sie im Hinweis im Abschnitt [Geltungsbereiche von Zugriffstoken](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="397c7-197">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="397c7-198">Die Blazor WebAssembly-Vorlage wird möglicherweise in einem zukünftigen Release von ASP.NET Core geändert werden, um diese Szenarios zu beheben.</span><span class="sxs-lookup"><span data-stu-id="397c7-198">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="397c7-199">Weitere Informationen finden Sie unter [Doppeltes Schema für den App-ID-URI in Blazor WASM-Vorlage (gehostet, Einzelorganisation) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="397c7-199">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="397c7-200">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="397c7-200">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="397c7-201">Der von der gehosteten Blazor-Vorlage festgelegte Bereich könnte den Host des App-ID-URI wiederholt haben.</span><span class="sxs-lookup"><span data-stu-id="397c7-201">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="397c7-202">Vergewissern Sie sich, dass der für die `DefaultAccessTokenScopes`-Sammlung konfigurierte Bereich in `Program.Main` (`Program.cs`) der *`Client`* -App korrekt ist.</span><span class="sxs-lookup"><span data-stu-id="397c7-202">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="397c7-203">Im Azure-Portal ist der **Umleitungs-URI** für die Plattformkonfiguration der *`Client`* -App für Apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="397c7-203">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="397c7-204">Wenn die *`Client`* -App an einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der *Server-API-App* im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="397c7-204">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="397c7-205">Wenn der Port nicht zuvor mit dem bekannten Port der *`Client`* -App konfiguriert wurde, kehren Sie zur *`Client`* -App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.</span><span class="sxs-lookup"><span data-stu-id="397c7-205">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="397c7-206">*`Server`* -App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="397c7-206">*`Server`* app configuration</span></span>

<span data-ttu-id="397c7-207">*Dieser Abschnitt bezieht sich auf die **`Server`** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="397c7-207">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="397c7-208">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="397c7-208">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="397c7-209">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs mit der Microsoft Identity Platform wird von folgenden Paketen bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="397c7-209">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="397c7-210">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der App übereinstimmt, im **Versionsverlauf** des Pakets auf NuGet.org ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="397c7-210">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="397c7-211">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs wird vom Paket [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="397c7-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="397c7-212">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="397c7-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="397c7-213">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="397c7-213">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="397c7-214">Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="397c7-214">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="397c7-215">Mit der <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A>-Methode werden Dienste konfiguriert, um die Web-API mit Microsoft Identity Platform v 2.0 zu schützen.</span><span class="sxs-lookup"><span data-stu-id="397c7-215">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="397c7-216">Diese Methode erwartet einen `AzureAdB2C`-Abschnitt in der App-Konfiguration mit den erforderlichen Einstellungen zum Initialisieren der Authentifizierungsoptionen.</span><span class="sxs-lookup"><span data-stu-id="397c7-216">This method expects an `AzureAdB2C` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="397c7-217">Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="397c7-217">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="397c7-218">Die <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>-Methode richtet die spezifischen Parameter im JWT-Bearerhandler ein, die zum Überprüfen der von Azure Active Directory B2C ausgegebenen Token erforderlich sind:</span><span class="sxs-lookup"><span data-stu-id="397c7-218">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

::: moniker-end

<span data-ttu-id="397c7-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> stellen Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="397c7-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="397c7-220">Die App versucht, Token auf eingehende Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="397c7-220">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="397c7-221">Jede Anforderung, die versucht, ohne die richtigen Anmeldeinformationen auf eine geschützte Ressource zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="397c7-221">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="397c7-222">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="397c7-222">User.Identity.Name</span></span>

<span data-ttu-id="397c7-223">`User.Identity.Name` wird standardmäßig nicht ausgefüllt.</span><span class="sxs-lookup"><span data-stu-id="397c7-223">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="397c7-224">So konfigurieren Sie die App, um den Wert vom `name`-Anspruchstyp zu empfangen:</span><span class="sxs-lookup"><span data-stu-id="397c7-224">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="397c7-225">Fügen Sie `Startup.cs` einen Namespace für <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> hinzu:</span><span class="sxs-lookup"><span data-stu-id="397c7-225">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="397c7-226">Konfigurieren Sie den <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="397c7-226">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="397c7-227">Konfigurieren Sie den <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="397c7-227">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="397c7-228">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="397c7-228">App settings</span></span>

<span data-ttu-id="397c7-229">Die Datei `appsettings.json` enthält die Optionen zum Konfigurieren des JWT-Bearerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="397c7-229">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="397c7-230">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="397c7-230">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="397c7-231">WeatherForecast-Controller</span><span class="sxs-lookup"><span data-stu-id="397c7-231">WeatherForecast controller</span></span>

<span data-ttu-id="397c7-232">Der WeatherForecast-Controller (*Controllers/WeatherForecastController.cs*) macht eine geschützte API mit auf den Controller angewendetem [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut verfügbar.</span><span class="sxs-lookup"><span data-stu-id="397c7-232">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="397c7-233">Es ist **wichtig**, Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="397c7-233">It's **important** to understand that:</span></span>

* <span data-ttu-id="397c7-234">Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="397c7-234">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="397c7-235">Das in der Blazor WebAssembly-App verwendete [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut dient nur als Hinweis für die App, dass der Benutzer autorisiert sein sollte, damit die App richtig funktioniert.</span><span class="sxs-lookup"><span data-stu-id="397c7-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="397c7-236">*`Client`* -App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="397c7-236">*`Client`* app configuration</span></span>

<span data-ttu-id="397c7-237">*Dieser Abschnitt bezieht sich auf die **`Client`** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="397c7-237">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="397c7-238">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="397c7-238">Authentication package</span></span>

<span data-ttu-id="397c7-239">Wenn eine App zum Verwenden eines B2C-Einzelkontos (`IndividualB2C`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="397c7-239">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="397c7-240">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="397c7-240">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="397c7-241">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="397c7-241">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="397c7-242">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="397c7-242">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="397c7-243">Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket transitiv hinzu.</span><span class="sxs-lookup"><span data-stu-id="397c7-243">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="397c7-244">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="397c7-244">Authentication service support</span></span>

<span data-ttu-id="397c7-245">Es wird Unterstützung für <xref:System.Net.Http.HttpClient>-Instanzen hinzugefügt, die bei Anforderungen an das Serverprojekt Zugriffstoken einschließen.</span><span class="sxs-lookup"><span data-stu-id="397c7-245">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="397c7-246">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="397c7-246">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="397c7-247">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="397c7-247">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="397c7-248">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="397c7-248">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="397c7-249">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="397c7-249">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="397c7-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="397c7-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="397c7-251">Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="397c7-251">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="397c7-252">Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration im Azure-Portal beim Registrieren der App abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="397c7-252">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="397c7-253">Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="397c7-253">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="397c7-254">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="397c7-254">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="397c7-255">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="397c7-255">Access token scopes</span></span>

<span data-ttu-id="397c7-256">Zu den Standard-Zugriffstokenbereichen zählen die Zugriffstokenbereiche, auf die Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="397c7-256">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="397c7-257">standardmäßig in der Anmeldeanforderung enthalten</span><span class="sxs-lookup"><span data-stu-id="397c7-257">Included by default in the sign in request.</span></span>
* <span data-ttu-id="397c7-258">zum Bereitstellen eines Zugriffstokens direkt nach der Authentifizierung verwendet</span><span class="sxs-lookup"><span data-stu-id="397c7-258">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="397c7-259">Alle Bereiche müssen gemäß den Azure Active Directory-Regeln zur selben App gehören.</span><span class="sxs-lookup"><span data-stu-id="397c7-259">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="397c7-260">Zusätzliche Bereiche können nach Bedarf für zusätzliche API-Apps hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="397c7-260">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="397c7-261">Die Blazor WebAssembly-Vorlage fügt dem App-ID-URI-Argument, das im Befehl `dotnet new` übergeben wird, automatisch ein `api://`-Schema hinzu.</span><span class="sxs-lookup"><span data-stu-id="397c7-261">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="397c7-262">Wenn eine App aus der Blazor-Projektvorlage generiert wird, überprüfen Sie, ob der Wert des Bereichs für das Standardzugriffstoken entweder den richtigen URI-Wert für die App-ID verwendet, den Sie im Azure-Portal bereitgestellt haben, oder einen Wert mit **einem** der folgenden Formate:</span><span class="sxs-lookup"><span data-stu-id="397c7-262">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="397c7-263">Wenn die Domäne des Herausgebers des Verzeichnisses als **vertrauenswürdig** gilt, weist der Bereich des Standardzugriffstoken in der Regel einen Wert ähnlich des folgenden Beispiels auf. Dabei ist `API.Access` der Name des Standardbereichs:</span><span class="sxs-lookup"><span data-stu-id="397c7-263">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="397c7-264">Überprüfen Sie, ob der Wert ein doppeltes Schema aufweist (`api://api://...`).</span><span class="sxs-lookup"><span data-stu-id="397c7-264">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="397c7-265">Wenn ein doppeltes Schema vorhanden ist, entfernen Sie das erste `api://`-Schema aus dem Wert.</span><span class="sxs-lookup"><span data-stu-id="397c7-265">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="397c7-266">Wenn die Domäne des Herausgebers des Verzeichnisses als **nicht vertrauenswürdig** gilt, weist der Bereich des Standardzugriffstoken in der Regel einen Wert ähnlich des folgenden Beispiels auf. Dabei ist `API.Access` der Name des Standardbereichs:</span><span class="sxs-lookup"><span data-stu-id="397c7-266">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="397c7-267">Überprüfen Sie, ob der Wert ein zusätzliches `api://`-Schema aufweist (`api://https://contoso.onmicrosoft.com/...`).</span><span class="sxs-lookup"><span data-stu-id="397c7-267">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="397c7-268">Wenn ein zusätzliches `api://`-Schema vorhanden ist, entfernen Sie das erste `api://`-Schema aus dem Wert.</span><span class="sxs-lookup"><span data-stu-id="397c7-268">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="397c7-269">Die Blazor WebAssembly-Vorlage wird möglicherweise in einem zukünftigen Release von ASP.NET Core geändert werden, um diese Szenarios zu beheben.</span><span class="sxs-lookup"><span data-stu-id="397c7-269">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="397c7-270">Weitere Informationen finden Sie unter [Doppeltes Schema für den App-ID-URI in Blazor WASM-Vorlage (gehostet, Einzelorganisation) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="397c7-270">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="397c7-271">Geben Sie zusätzliche Bereiche mit `AdditionalScopesToConsent` an:</span><span class="sxs-lookup"><span data-stu-id="397c7-271">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="397c7-272">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:</span><span class="sxs-lookup"><span data-stu-id="397c7-272">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="397c7-273">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="397c7-273">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="397c7-274">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="397c7-274">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="397c7-275">Anmeldemodus</span><span class="sxs-lookup"><span data-stu-id="397c7-275">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="397c7-276">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="397c7-276">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="397c7-277">Indexseite</span><span class="sxs-lookup"><span data-stu-id="397c7-277">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="397c7-278">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="397c7-278">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="397c7-279">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="397c7-279">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="397c7-280">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="397c7-280">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="397c7-281">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="397c7-281">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="397c7-282">FetchData-Komponente</span><span class="sxs-lookup"><span data-stu-id="397c7-282">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="397c7-283">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="397c7-283">Run the app</span></span>

<span data-ttu-id="397c7-284">Führen Sie die App aus dem Serverprojekt aus.</span><span class="sxs-lookup"><span data-stu-id="397c7-284">Run the app from the Server project.</span></span> <span data-ttu-id="397c7-285">Wenn Sie Visual Studio verwenden, haben Sie folgende beiden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="397c7-285">When using Visual Studio, either:</span></span>

* <span data-ttu-id="397c7-286">Wählen Sie in der Dropdownliste **Startprojekte** in der Symbolleiste die *Server-API-App* aus, und klicken Sie auf **Ausführen**.</span><span class="sxs-lookup"><span data-stu-id="397c7-286">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="397c7-287">Wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und klicken Sie in der Symbolleiste auf **Ausführen**, oder starten Sie die App über das Menü **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="397c7-287">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="397c7-288">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="397c7-288">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="397c7-289">Erstellen einer benutzerdefinierten Version der Authentication.MSAL-JavaScript-Bibliothek</span><span class="sxs-lookup"><span data-stu-id="397c7-289">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="397c7-290">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="397c7-290">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="397c7-291">Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="397c7-291">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="397c7-292">Tutorial: Registrieren Sie eine Anwendung in Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="397c7-292">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="397c7-293">Dokumentation zu Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="397c7-293">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
