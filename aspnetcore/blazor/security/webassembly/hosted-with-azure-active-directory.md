---
title: Sichern einer gehosteten ASP.NET Core :::no-loc(Blazor WebAssembly):::-App mit Azure Active Directory
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie eine gehostete ASP.NET Core :::no-loc(Blazor WebAssembly):::-App mit Azure Active Directory sichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 17f96be762ece8c59577445eb2ae630a8ee3b3dd
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234477"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="a11ee-103">Sichern einer gehosteten ASP.NET Core :::no-loc(Blazor WebAssembly):::-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="a11ee-103">Secure an ASP.NET Core :::no-loc(Blazor WebAssembly)::: hosted app with Azure Active Directory</span></span>

<span data-ttu-id="a11ee-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a11ee-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a11ee-105">In diesem Artikel wird beschrieben, wie Sie eine [gehostete :::no-loc(Blazor WebAssembly):::-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="a11ee-105">This article describes how to create a [hosted :::no-loc(Blazor WebAssembly)::: app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a11ee-106">Visual Studio konfiguriert :::no-loc(Blazor WebAssembly):::-Apps, die in Visual Studio erstellt und für die Unterstützung von Konten in einem AAD-Organisationsverzeichnis konfiguriert wurden, bei der Projektgenerierung nicht ordnungsgemäß.</span><span class="sxs-lookup"><span data-stu-id="a11ee-106">For :::no-loc(Blazor WebAssembly)::: apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="a11ee-107">Dieses Problem wird in einem zukünftigen Release von Visual Studio behoben.</span><span class="sxs-lookup"><span data-stu-id="a11ee-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="a11ee-108">In diesem Artikel wird gezeigt, wie Sie die App mit dem Befehl `dotnet new` der .NET Core-CLI erstellen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="a11ee-109">Wenn Sie die App lieber mit Visual Studio erstellen möchten, auch wenn die IDE noch nicht mit den neuesten :::no-loc(Blazor):::-Vorlagen in ASP.NET Core 5.0 aktualisiert wurde, lesen Sie die einzelnen Abschnitte in diesem Artikel, und bestätigen oder aktualisieren Sie die Konfiguration der App, nachdem sie in Visual Studio erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="a11ee-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest :::no-loc(Blazor)::: templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="a11ee-110">Registrieren von Apps in AAD und Erstellen einer Lösung</span><span class="sxs-lookup"><span data-stu-id="a11ee-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a11ee-111">Erstellen eines Mandanten</span><span class="sxs-lookup"><span data-stu-id="a11ee-111">Create a tenant</span></span>

<span data-ttu-id="a11ee-112">Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten eines Mandanten](/azure/active-directory/develop/quickstart-create-new-tenant), um einen Mandanten in AAD zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a11ee-113">Registrieren einer Server-API-App</span><span class="sxs-lookup"><span data-stu-id="a11ee-113">Register a server API app</span></span>

<span data-ttu-id="a11ee-114">Befolgen Sie die Anweisungen unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und in den anschließenden Azure AAD-Themen, um eine AAD-App für die *Server-API-App* zu registrieren, und tun Sie danach Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a11ee-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="a11ee-115">Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-115">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="a11ee-116">Geben Sie einen **Namen** für die App an (z. B. **:::no-loc(Blazor Server)::: AAD** ).</span><span class="sxs-lookup"><span data-stu-id="a11ee-116">Provide a **Name** for the app (for example, **:::no-loc(Blazor Server)::: AAD** ).</span></span>
1. <span data-ttu-id="a11ee-117">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="a11ee-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="a11ee-118">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a11ee-119">Die *Server-API-App* erfordert in diesem Szenario keinen **Umleitungs-URI**. Belassen Sie daher die Dropdownauswahl bei **Web** , und geben Sie keinen Umleitungs-URI ein.</span><span class="sxs-lookup"><span data-stu-id="a11ee-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a11ee-120">Deaktivieren Sie das Kontrollkästchen bei **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a11ee-121">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-121">Select **Register**.</span></span>

<span data-ttu-id="a11ee-122">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="a11ee-122">Record the following information:</span></span>

* <span data-ttu-id="a11ee-123">Anwendungs-ID (Client-ID) der *Server-API-App* (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span><span class="sxs-lookup"><span data-stu-id="a11ee-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="a11ee-124">Verzeichnis-ID (Mandanten-ID) (z. B. `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span><span class="sxs-lookup"><span data-stu-id="a11ee-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="a11ee-125">Primäre, Herausgeber- oder Mandantendomäne für AAD (z. B. `contoso.onmicrosoft.com`): Die Domäne ist als **Herausgeberdomäne** auf dem Blatt **Branding** des Azure-Portals für die registrierte App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a11ee-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="a11ee-126">Entfernen Sie unter **API-Berechtigungen** die Berechtigung **Microsoft Graph** > **User.Read** , da die App keine Anmeldung oder Zugriff auf Benutzerprofile benötigt.</span><span class="sxs-lookup"><span data-stu-id="a11ee-126">In **API permissions** , remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="a11ee-127">Gehen Sie unter **Eine API verfügbar machen** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="a11ee-127">In **Expose an API** :</span></span>

1. <span data-ttu-id="a11ee-128">Wählen Sie **Bereich hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a11ee-129">Wählen Sie **Speichern und fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="a11ee-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a11ee-130">Geben Sie einen **Bereichsnamen** an (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a11ee-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a11ee-131">Geben Sie einen **Anzeigenamen der Administratorzustimmung** an (z. B. `Access API`).</span><span class="sxs-lookup"><span data-stu-id="a11ee-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a11ee-132">Geben Sie eine **Beschreibung der Administratorzustimmung** an (z. B. `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="a11ee-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a11ee-133">Vergewissern Sie sich, dass **Zustand** auf **Aktiviert** gesetzt ist.</span><span class="sxs-lookup"><span data-stu-id="a11ee-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a11ee-134">Wählen Sie **Bereich hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="a11ee-134">Select **Add scope**.</span></span>

<span data-ttu-id="a11ee-135">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="a11ee-135">Record the following information:</span></span>

* <span data-ttu-id="a11ee-136">App-ID-URI (z. B. `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` oder der von Ihnen angegebene benutzerdefinierte Wert)</span><span class="sxs-lookup"><span data-stu-id="a11ee-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="a11ee-137">Bereichsname (beispielsweise `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="a11ee-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a11ee-138">Registrieren einer Client-App</span><span class="sxs-lookup"><span data-stu-id="a11ee-138">Register a client app</span></span>

<span data-ttu-id="a11ee-139">Befolgen Sie die Anweisungen unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app) und in den anschließenden Azure AAD-Themen, um eine AAD-App für die *`Client`* -App zu registrieren, und tun Sie danach Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a11ee-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="a11ee-140">Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="a11ee-140">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="a11ee-141">Geben Sie einen **Namen** für die App an (z. B. **:::no-loc(Blazor)::: Client AAD** ).</span><span class="sxs-lookup"><span data-stu-id="a11ee-141">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD** ).</span></span>
1. <span data-ttu-id="a11ee-142">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="a11ee-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="a11ee-143">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a11ee-144">Treffen Sie für **Umleitungs-URI** die Dropdownauswahl **Single-Page-Anwendung (SPA)** , und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="a11ee-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a11ee-145">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="a11ee-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a11ee-146">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="a11ee-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a11ee-147">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der *`Server`* -App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a11ee-148">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="a11ee-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a11ee-149">Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="a11ee-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a11ee-150">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a11ee-151">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-151">Select **Register**.</span></span>

<span data-ttu-id="a11ee-152">Notieren Sie sich die Anwendungs-ID (Client-ID) der *`Client`* -App (z. B. `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="a11ee-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="a11ee-153">In **Authentifizierung** > **Plattformkonfigurationen** > **Single-Page-Anwendung (SPA)** :</span><span class="sxs-lookup"><span data-stu-id="a11ee-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="a11ee-154">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="a11ee-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a11ee-155">Stellen Sie sicher, dass für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token** **nicht** aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="a11ee-155">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="a11ee-156">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="a11ee-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a11ee-157">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="a11ee-158">Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="a11ee-158">In **Azure Active Directory** > **App registrations** , select **New registration**.</span></span>
1. <span data-ttu-id="a11ee-159">Geben Sie einen **Namen** für die App an (z. B. **:::no-loc(Blazor)::: Client AAD** ).</span><span class="sxs-lookup"><span data-stu-id="a11ee-159">Provide a **Name** for the app (for example, **:::no-loc(Blazor)::: Client AAD** ).</span></span>
1. <span data-ttu-id="a11ee-160">Wählen Sie einen **Unterstützten Kontotyp** aus.</span><span class="sxs-lookup"><span data-stu-id="a11ee-160">Choose a **Supported account types**.</span></span> <span data-ttu-id="a11ee-161">Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="a11ee-162">Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web** , und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="a11ee-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a11ee-163">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="a11ee-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a11ee-164">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="a11ee-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a11ee-165">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der *`Server`* -App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a11ee-166">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="a11ee-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a11ee-167">Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="a11ee-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a11ee-168">Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a11ee-169">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-169">Select **Register**.</span></span>

<span data-ttu-id="a11ee-170">Notieren Sie sich die Anwendungs-ID (Client-ID) der *`Client`* -App (z. B. `4369008b-21fa-427c-abaa-9b53bf58e538`).</span><span class="sxs-lookup"><span data-stu-id="a11ee-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="a11ee-171">In **Authentifizierung** > **Plattformkonfigurationen** > **Web** :</span><span class="sxs-lookup"><span data-stu-id="a11ee-171">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="a11ee-172">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="a11ee-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a11ee-173">Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-173">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a11ee-174">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="a11ee-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a11ee-175">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="a11ee-176">Gehen Sie unter **API-Berechtigungen** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="a11ee-176">In **API permissions** :</span></span>

1. <span data-ttu-id="a11ee-177">Sorgen Sie dafür, dass die App über die Berechtigung **Microsoft Graph** > **User.Read** verfügt.</span><span class="sxs-lookup"><span data-stu-id="a11ee-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="a11ee-178">Klicken Sie auf **Berechtigung hinzufügen** und dann auf **Meine APIs**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-178">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a11ee-179">Wählen Sie die *Server-API-App* aus der Spalte **Name** aus (z. B. **:::no-loc(Blazor Server)::: AAD** ).</span><span class="sxs-lookup"><span data-stu-id="a11ee-179">Select the *Server API app* from the **Name** column (for example, **:::no-loc(Blazor Server)::: AAD** ).</span></span>
1. <span data-ttu-id="a11ee-180">Öffnen Sie die **API** -Liste.</span><span class="sxs-lookup"><span data-stu-id="a11ee-180">Open the **API** list.</span></span>
1. <span data-ttu-id="a11ee-181">Ermöglichen Sie den Zugriff auf die API (z. B. `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a11ee-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a11ee-182">Wählen Sie **Berechtigungen hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="a11ee-182">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a11ee-183">Klicken Sie auf die Schaltfläche **Administratoreinwilligung für {MANDANTENNAME} erteilen**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="a11ee-184">Klicken Sie auf **Ja** , um zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a11ee-185">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="a11ee-185">Create the app</span></span>

<span data-ttu-id="a11ee-186">Verwenden Sie einen leeren Ordner, ersetzen Sie im folgenden Befehl die Platzhalter durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="a11ee-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="a11ee-187">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="a11ee-187">Placeholder</span></span>                  | <span data-ttu-id="a11ee-188">Name im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="a11ee-188">Azure portal name</span></span>                                     | <span data-ttu-id="a11ee-189">Beispiel</span><span class="sxs-lookup"><span data-stu-id="a11ee-189">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `:::no-loc(Blazor):::Sample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="a11ee-190">Anwendungs-ID (Client-ID) für die *`Client`* -App</span><span class="sxs-lookup"><span data-stu-id="a11ee-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="a11ee-191">Bereichsname</span><span class="sxs-lookup"><span data-stu-id="a11ee-191">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="a11ee-192">Anwendungs-ID (Client-ID) für die *Server-API-App*</span><span class="sxs-lookup"><span data-stu-id="a11ee-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="a11ee-193">Anwendungs-ID-URI&dagger;</span><span class="sxs-lookup"><span data-stu-id="a11ee-193">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="a11ee-194">Primäre, Herausgeber- oder Mandantendomäne</span><span class="sxs-lookup"><span data-stu-id="a11ee-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="a11ee-195">Verzeichnis-ID (Mandant)</span><span class="sxs-lookup"><span data-stu-id="a11ee-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="a11ee-196">&dagger;Die :::no-loc(Blazor WebAssembly):::-Vorlage fügt dem App-ID-URI-Argument, das im Befehl `dotnet new` übergeben wird, automatisch ein `api://`-Schema hinzu.</span><span class="sxs-lookup"><span data-stu-id="a11ee-196">&dagger;The :::no-loc(Blazor WebAssembly)::: template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="a11ee-197">Wenn Sie den App-ID-URI für den Platzhalter `{SERVER API APP ID URI}` bereitstellen und das Schema `api://` lautet, entfernen Sie das Schema (`api://`) aus dem Argument. Dies wird anhand des Beispielwerts in der obigen Tabelle gezeigt.</span><span class="sxs-lookup"><span data-stu-id="a11ee-197">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="a11ee-198">Wenn der App-ID-URI ein benutzerdefinierter Wert ist oder ein anderes Schema aufweist (z. B. `https://` für eine nicht vertrauenswürdige Herausgeberdomäne wie `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), müssen Sie den Standard-URI für den Geltungsbereich manuell aktualisieren und das Schema `api://` entfernen, nachdem die *`Client`* -App von der Vorlage erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="a11ee-198">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="a11ee-199">Weitere Informationen finden Sie im Hinweis im Abschnitt [Geltungsbereiche von Zugriffstoken](#access-token-scopes).</span><span class="sxs-lookup"><span data-stu-id="a11ee-199">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="a11ee-200">Die :::no-loc(Blazor WebAssembly):::-Vorlage wird möglicherweise in einem zukünftigen Release von ASP.NET Core geändert werden, um diese Szenarios zu beheben.</span><span class="sxs-lookup"><span data-stu-id="a11ee-200">The :::no-loc(Blazor WebAssembly)::: template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="a11ee-201">Weitere Informationen finden Sie unter [Doppeltes Schema für den App-ID-URI in :::no-loc(Blazor)::: WASM-Vorlage (gehostet, Einzelorganisation) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="a11ee-201">For more information, see [Double scheme for App ID URI with :::no-loc(Blazor)::: WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="a11ee-202">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="a11ee-202">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a11ee-203">Eine Konfigurationsänderung ist möglicherweise erforderlich, wenn Sie einen Azure-Mandanten mit einer nicht überprüften Herausgeberdomäne verwenden, was im Abschnitt [App-Einstellungen](#app-settings) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="a11ee-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="a11ee-204">Eine Konfigurationsänderung ist möglicherweise erforderlich, wenn Sie einen Azure-Mandanten mit einer nicht überprüften Herausgeberdomäne verwenden, was im Abschnitt [Zugriffstokenbereiche](#access-token-scopes) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="a11ee-204">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="a11ee-205">Im Azure-Portal ist der **Umleitungs-URI** für die Plattformkonfiguration der *`Client`* -App für Apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a11ee-205">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a11ee-206">Wenn die *`Client`* -App an einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der *Server-API-App* im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-206">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a11ee-207">Wenn der Port nicht zuvor mit dem bekannten Port der *`Client`* -App konfiguriert wurde, kehren Sie zur *`Client`* -App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.</span><span class="sxs-lookup"><span data-stu-id="a11ee-207">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a11ee-208">*`Server`* -App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a11ee-208">*`Server`* app configuration</span></span>

<span data-ttu-id="a11ee-209">*Dieser Abschnitt bezieht sich auf die **`Server`** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="a11ee-209">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a11ee-210">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="a11ee-210">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a11ee-211">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs mit der Microsoft :::no-loc(Identity)::: Platform wird von folgenden Paketen bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="a11ee-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft :::no-loc(Identity)::: Platform is provided by the following packages:</span></span>

* [`Microsoft.:::no-loc(Identity):::.Web`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web)
* [`Microsoft.:::no-loc(Identity):::.Web.UI`](https://www.nuget.org/packages/Microsoft.:::no-loc(Identity):::.Web.UI)

```xml
<PackageReference Include="Microsoft.:::no-loc(Identity):::.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.:::no-loc(Identity):::.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="a11ee-212">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der App übereinstimmt, im **Versionsverlauf** des Pakets auf NuGet.org ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="a11ee-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a11ee-213">Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs wird vom Paket [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="a11ee-213">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="a11ee-214">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="a11ee-214">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="a11ee-215">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="a11ee-215">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a11ee-216">Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="a11ee-216">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a11ee-217">Mit der <xref:Microsoft.:::no-loc(Identity):::.Web.Microsoft:::no-loc(Identity):::WebApiAuthenticationBuilderExtensions.AddMicrosoft:::no-loc(Identity):::WebApi%2A>-Methode werden Dienste konfiguriert, um die Web-API mit Microsoft :::no-loc(Identity)::: Platform v 2.0 zu schützen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-217">The <xref:Microsoft.:::no-loc(Identity):::.Web.Microsoft:::no-loc(Identity):::WebApiAuthenticationBuilderExtensions.AddMicrosoft:::no-loc(Identity):::WebApi%2A> method configures services to protect the web API with Microsoft :::no-loc(Identity)::: Platform v2.0.</span></span> <span data-ttu-id="a11ee-218">Diese Methode erwartet einen `AzureAd`-Abschnitt in der App-Konfiguration mit den erforderlichen Einstellungen zum Initialisieren der Authentifizierungsoptionen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-218">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoft:::no-loc(Identity):::WebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a11ee-219">Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode.</span><span class="sxs-lookup"><span data-stu-id="a11ee-219">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a11ee-220">Die <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>-Methode richtet die spezifischen Parameter im JWT-Bearerhandler ein, die zum Überprüfen der von Azure Active Directory ausgegebenen Token erforderlich sind:</span><span class="sxs-lookup"><span data-stu-id="a11ee-220">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="a11ee-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> stellen Folgendes sicher:</span><span class="sxs-lookup"><span data-stu-id="a11ee-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="a11ee-222">Die App versucht, Token auf eingehende Anforderungen zu analysieren und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-222">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a11ee-223">Jede Anforderung, die versucht, ohne die richtigen Anmeldeinformationen auf eine geschützte Ressource zuzugreifen, schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="a11ee-223">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="a11ee-224">User.:::no-loc(Identity):::.Name</span><span class="sxs-lookup"><span data-stu-id="a11ee-224">User.:::no-loc(Identity):::.Name</span></span>

<span data-ttu-id="a11ee-225">Standardmäßig füllt die *`Server`* -App-API `User.:::no-loc(Identity):::.Name` mit dem Wert aus dem `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`-Anspruchstyp (z. B. `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`) auf.</span><span class="sxs-lookup"><span data-stu-id="a11ee-225">By default, the *`Server`* app API populates `User.:::no-loc(Identity):::.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="a11ee-226">Konfigurieren Sie <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`, um die App so zu konfigurieren, dass sie den Wert aus dem `name`-Anspruchstyp empfängt:</span><span class="sxs-lookup"><span data-stu-id="a11ee-226">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.:::no-loc(Identity):::Model.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="a11ee-227">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="a11ee-227">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="a11ee-228">Die Datei `:::no-loc(appsettings.json):::` enthält die Optionen zum Konfigurieren des JWT-Bearerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="a11ee-228">The `:::no-loc(appsettings.json):::` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="a11ee-229">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a11ee-229">Example:</span></span>

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

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="a11ee-230">Die Datei `:::no-loc(appsettings.json):::` enthält die Optionen zum Konfigurieren des JWT-Bearerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="a11ee-230">The `:::no-loc(appsettings.json):::` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="a11ee-231">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a11ee-231">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="a11ee-232">WeatherForecast-Controller</span><span class="sxs-lookup"><span data-stu-id="a11ee-232">WeatherForecast controller</span></span>

<span data-ttu-id="a11ee-233">Der WeatherForecast-Controller ( *Controllers/WeatherForecastController.cs* ) macht eine geschützte API mit auf den Controller angewendetem [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a11ee-233">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="a11ee-234">Es ist **wichtig** , Folgendes zu verstehen:</span><span class="sxs-lookup"><span data-stu-id="a11ee-234">It's **important** to understand that:</span></span>

* <span data-ttu-id="a11ee-235">Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a11ee-236">Das in der :::no-loc(Blazor WebAssembly):::-App verwendete [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut dient nur als Hinweis für die App, dass der Benutzer autorisiert sein sollte, damit die App richtig funktioniert.</span><span class="sxs-lookup"><span data-stu-id="a11ee-236">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the :::no-loc(Blazor WebAssembly)::: app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="a11ee-237">*`Client`* -App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a11ee-237">*`Client`* app configuration</span></span>

<span data-ttu-id="a11ee-238">*Dieser Abschnitt bezieht sich auf die **`Client`** -App der Lösung.*</span><span class="sxs-lookup"><span data-stu-id="a11ee-238">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a11ee-239">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="a11ee-239">Authentication package</span></span>

<span data-ttu-id="a11ee-240">Wenn eine App zum Verwenden von Geschäfts-, Schul- oder Unikonten (`SingleOrg`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="a11ee-240">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="a11ee-241">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-241">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a11ee-242">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="a11ee-242">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="a11ee-243">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="a11ee-243">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="a11ee-244">Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket transitiv hinzu.</span><span class="sxs-lookup"><span data-stu-id="a11ee-244">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a11ee-245">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="a11ee-245">Authentication service support</span></span>

<span data-ttu-id="a11ee-246">Es wird Unterstützung für <xref:System.Net.Http.HttpClient>-Instanzen hinzugefügt, die bei Anforderungen an das Serverprojekt Zugriffstoken einschließen.</span><span class="sxs-lookup"><span data-stu-id="a11ee-246">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="a11ee-247">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a11ee-247">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="a11ee-248">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `:::no-loc(Blazor):::Sample.ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="a11ee-248">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `:::no-loc(Blazor):::Sample.ServerAPI`).</span></span>

<span data-ttu-id="a11ee-249">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="a11ee-249">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="a11ee-250">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="a11ee-250">This method sets up the services required for the app to interact with the :::no-loc(Identity)::: Provider (IP).</span></span>

<span data-ttu-id="a11ee-251">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="a11ee-251">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a11ee-252">Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="a11ee-252">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a11ee-253">Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration im Azure-Portal beim Registrieren der App abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="a11ee-253">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="a11ee-254">Die Konfiguration wird durch die Datei `wwwroot/:::no-loc(appsettings.json):::` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="a11ee-254">Configuration is supplied by the `wwwroot/:::no-loc(appsettings.json):::` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a11ee-255">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a11ee-255">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="a11ee-256">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="a11ee-256">Access token scopes</span></span>

<span data-ttu-id="a11ee-257">Zu den Standard-Zugriffstokenbereichen zählen die Zugriffstokenbereiche, auf die Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="a11ee-257">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a11ee-258">standardmäßig in der Anmeldeanforderung enthalten</span><span class="sxs-lookup"><span data-stu-id="a11ee-258">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a11ee-259">zum Bereitstellen eines Zugriffstokens direkt nach der Authentifizierung verwendet</span><span class="sxs-lookup"><span data-stu-id="a11ee-259">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a11ee-260">Alle Bereiche müssen gemäß den Azure Active Directory-Regeln zur selben App gehören.</span><span class="sxs-lookup"><span data-stu-id="a11ee-260">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a11ee-261">Zusätzliche Bereiche können nach Bedarf für zusätzliche API-Apps hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="a11ee-261">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a11ee-262">Die :::no-loc(Blazor WebAssembly):::-Vorlage fügt dem App-ID-URI-Argument, das im Befehl `dotnet new` übergeben wird, automatisch ein `api://`-Schema hinzu.</span><span class="sxs-lookup"><span data-stu-id="a11ee-262">The :::no-loc(Blazor WebAssembly)::: template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="a11ee-263">Wenn eine App aus der :::no-loc(Blazor):::-Projektvorlage generiert wird, überprüfen Sie, ob der Wert des Bereichs für das Standardzugriffstoken entweder den richtigen URI-Wert für die App-ID verwendet, den Sie im Azure-Portal bereitgestellt haben, oder einen Wert mit **einem** der folgenden Formate:</span><span class="sxs-lookup"><span data-stu-id="a11ee-263">When generating an app from the :::no-loc(Blazor)::: project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="a11ee-264">Wenn die Domäne des Herausgebers des Verzeichnisses als **vertrauenswürdig** gilt, weist der Bereich des Standardzugriffstoken in der Regel einen Wert ähnlich des folgenden Beispiels auf. Dabei ist `API.Access` der Name des Standardbereichs:</span><span class="sxs-lookup"><span data-stu-id="a11ee-264">When the publisher domain of the directory is **trusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="a11ee-265">Überprüfen Sie, ob der Wert ein doppeltes Schema aufweist (`api://api://...`).</span><span class="sxs-lookup"><span data-stu-id="a11ee-265">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="a11ee-266">Wenn ein doppeltes Schema vorhanden ist, entfernen Sie das erste `api://`-Schema aus dem Wert.</span><span class="sxs-lookup"><span data-stu-id="a11ee-266">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="a11ee-267">Wenn die Domäne des Herausgebers des Verzeichnisses als **nicht vertrauenswürdig** gilt, weist der Bereich des Standardzugriffstoken in der Regel einen Wert ähnlich des folgenden Beispiels auf. Dabei ist `API.Access` der Name des Standardbereichs:</span><span class="sxs-lookup"><span data-stu-id="a11ee-267">When the publisher domain of the directory is **untrusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="a11ee-268">Überprüfen Sie, ob der Wert ein zusätzliches `api://`-Schema aufweist (`api://https://contoso.onmicrosoft.com/...`).</span><span class="sxs-lookup"><span data-stu-id="a11ee-268">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="a11ee-269">Wenn ein zusätzliches `api://`-Schema vorhanden ist, entfernen Sie das erste `api://`-Schema aus dem Wert.</span><span class="sxs-lookup"><span data-stu-id="a11ee-269">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="a11ee-270">Die :::no-loc(Blazor WebAssembly):::-Vorlage wird möglicherweise in einem zukünftigen Release von ASP.NET Core geändert werden, um diese Szenarios zu beheben.</span><span class="sxs-lookup"><span data-stu-id="a11ee-270">The :::no-loc(Blazor WebAssembly)::: template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="a11ee-271">Weitere Informationen finden Sie unter [Doppeltes Schema für den App-ID-URI in :::no-loc(Blazor)::: WASM-Vorlage (gehostet, Einzelorganisation) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="a11ee-271">For more information, see [Double scheme for App ID URI with :::no-loc(Blazor)::: WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="a11ee-272">Geben Sie zusätzliche Bereiche mit `AdditionalScopesToConsent` an:</span><span class="sxs-lookup"><span data-stu-id="a11ee-272">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="a11ee-273">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios* :</span><span class="sxs-lookup"><span data-stu-id="a11ee-273">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a11ee-274">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="a11ee-274">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a11ee-275">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="a11ee-275">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="a11ee-276">Anmeldemodus</span><span class="sxs-lookup"><span data-stu-id="a11ee-276">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="a11ee-277">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="a11ee-277">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a11ee-278">Indexseite</span><span class="sxs-lookup"><span data-stu-id="a11ee-278">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="a11ee-279">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="a11ee-279">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a11ee-280">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="a11ee-280">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a11ee-281">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="a11ee-281">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a11ee-282">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="a11ee-282">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a11ee-283">FetchData-Komponente</span><span class="sxs-lookup"><span data-stu-id="a11ee-283">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a11ee-284">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="a11ee-284">Run the app</span></span>

<span data-ttu-id="a11ee-285">Führen Sie die App aus dem Serverprojekt aus.</span><span class="sxs-lookup"><span data-stu-id="a11ee-285">Run the app from the Server project.</span></span> <span data-ttu-id="a11ee-286">Wenn Sie Visual Studio verwenden, haben Sie folgende beiden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="a11ee-286">When using Visual Studio, either:</span></span>

* <span data-ttu-id="a11ee-287">Wählen Sie in der Dropdownliste **Startprojekte** in der Symbolleiste die *Server-API-App* aus, und klicken Sie auf **Ausführen**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-287">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="a11ee-288">Wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und klicken Sie in der Symbolleiste auf **Ausführen** , oder starten Sie die App über das Menü **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="a11ee-288">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a11ee-289">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a11ee-289">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a11ee-290">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="a11ee-290">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="a11ee-291">Dokumentation zu Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="a11ee-291">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
