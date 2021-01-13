---
title: Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory B2C
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie eine eigenständige Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory B2C sichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 679f14642f4a611a5e65a7f472c68663fc1fb16b
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97764694"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="0020c-103">Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="0020c-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="0020c-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0020c-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0020c-105">In diesem Artikel wird beschrieben, wie Sie eine [eigenständige Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="0020c-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

<span data-ttu-id="0020c-106">Erstellen Sie einen Mandanten, oder identifizieren Sie einen vorhandenen B2C-Mandanten für die App, die im Azure-Portal verwendet werden soll. Befolgen Sie hierzu die Anweisungen im Tutorial [Erstellen eines Azure Active Directory B2C-Mandanten](/azure/active-directory-b2c/tutorial-create-tenant).</span><span class="sxs-lookup"><span data-stu-id="0020c-106">Create a tenant or identify an existing B2C tenant for the app to use in the Azure portal by following the guidance in the [Create an AAD B2C tenant (Azure documentation)](/azure/active-directory-b2c/tutorial-create-tenant) article.</span></span> <span data-ttu-id="0020c-107">Kehren Sie direkt nach dem Erstellen oder Identifizieren eines zu verwendenden Mandanten zu diesem Artikel zurück.</span><span class="sxs-lookup"><span data-stu-id="0020c-107">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="0020c-108">Notieren Sie sich folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="0020c-108">Record the following information:</span></span>

* <span data-ttu-id="0020c-109">AAD B2C-Instanz (z. B. `https://contoso.b2clogin.com/`, wobei der nachgestellte Schrägstrich enthalten ist): Die Instanz ist das Schema und der Host einer Azure-B2B-App-Registrierung, die Sie im Fenster **Endpunkte** auf der Seite **App-Registrierungen** im Azure-Portal finden.</span><span class="sxs-lookup"><span data-stu-id="0020c-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash): The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>
* <span data-ttu-id="0020c-110">Primäre, Herausgeber- oder Mandantendomäne für AAD B2C (z. B. `contoso.onmicrosoft.com`): Die Domäne ist als **Herausgeberdomäne** auf dem Blatt **Branding** des Azure-Portals für die registrierte App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="0020c-110">AAD B2C Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="0020c-111">Registrieren einer AAD B2C-App:</span><span class="sxs-lookup"><span data-stu-id="0020c-111">Register an AAD B2C app:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="0020c-112">Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="0020c-112">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="0020c-113">Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="0020c-113">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="0020c-114">Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisationsverzeichnis oder ein beliebiger Identitätsanbieter Wählen Sie diese Option aus, um Benutzer bei Azure AD B2C zu authentifizieren.**</span><span class="sxs-lookup"><span data-stu-id="0020c-114">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="0020c-115">Treffen Sie für **Umleitungs-URI** die Dropdownauswahl **Single-Page-Anwendung (SPA)** , und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="0020c-115">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="0020c-116">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="0020c-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="0020c-117">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="0020c-117">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="0020c-118">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="0020c-118">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="0020c-119">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="0020c-119">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="0020c-120">Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="0020c-120">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="0020c-121">Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="0020c-121">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="0020c-122">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="0020c-122">Select **Register**.</span></span>

<span data-ttu-id="0020c-123">Notieren Sie sich die Anwendungs-ID (Client-ID) (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="0020c-123">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="0020c-124">In **Authentifizierung** > **Plattformkonfigurationen** > **Single-Page-Anwendung (SPA)** :</span><span class="sxs-lookup"><span data-stu-id="0020c-124">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="0020c-125">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0020c-125">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="0020c-126">Stellen Sie sicher, dass für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token** **nicht** aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="0020c-126">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="0020c-127">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="0020c-127">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="0020c-128">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="0020c-128">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="0020c-129">Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="0020c-129">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="0020c-130">Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="0020c-130">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="0020c-131">Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisationsverzeichnis oder ein beliebiger Identitätsanbieter Wählen Sie diese Option aus, um Benutzer bei Azure AD B2C zu authentifizieren.**</span><span class="sxs-lookup"><span data-stu-id="0020c-131">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="0020c-132">Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="0020c-132">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="0020c-133">Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001.</span><span class="sxs-lookup"><span data-stu-id="0020c-133">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="0020c-134">Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App.</span><span class="sxs-lookup"><span data-stu-id="0020c-134">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="0020c-135">Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="0020c-135">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="0020c-136">Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI.</span><span class="sxs-lookup"><span data-stu-id="0020c-136">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="0020c-137">Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.</span><span class="sxs-lookup"><span data-stu-id="0020c-137">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="0020c-138">Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="0020c-138">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="0020c-139">Wählen Sie **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="0020c-139">Select **Register**.</span></span>

<span data-ttu-id="0020c-140">Notieren Sie sich die Anwendungs-ID (Client-ID) (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span><span class="sxs-lookup"><span data-stu-id="0020c-140">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="0020c-141">In **Authentifizierung** > **Plattformkonfigurationen** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="0020c-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="0020c-142">Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="0020c-142">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="0020c-143">Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.</span><span class="sxs-lookup"><span data-stu-id="0020c-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="0020c-144">Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="0020c-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="0020c-145">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="0020c-145">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="0020c-146">Gehen Sie unter **Startseite** > **Azure AD B2C** > **Benutzerflows** wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="0020c-146">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="0020c-147">Erstellen Sie einen Benutzerflow für die Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="0020c-147">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="0020c-148">Wählen Sie mindestens das Benutzerattribut **Anwendungsansprüche** > **Anzeigename** aus, um `context.User.Identity.Name` in der `LoginDisplay`-Komponente (`Shared/LoginDisplay.razor`) auszufüllen.</span><span class="sxs-lookup"><span data-stu-id="0020c-148">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="0020c-149">Notieren Sie sich den Namen des für die App erstellten Benutzerflows für die Registrierung und Anmeldung (z. B. `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="0020c-149">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="0020c-150">Verwenden Sie einen leeren Ordner, ersetzen Sie im folgenden Befehl die Platzhalter durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="0020c-150">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="0020c-151">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="0020c-151">Placeholder</span></span>                   | <span data-ttu-id="0020c-152">Name im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="0020c-152">Azure portal name</span></span>               | <span data-ttu-id="0020c-153">Beispiel</span><span class="sxs-lookup"><span data-stu-id="0020c-153">Example</span></span>                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="0020c-154">Instanz</span><span class="sxs-lookup"><span data-stu-id="0020c-154">Instance</span></span>                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | <span data-ttu-id="0020c-155">Anwendungs-ID (Client)</span><span class="sxs-lookup"><span data-stu-id="0020c-155">Application (client) ID</span></span>         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="0020c-156">Benutzerflow für die Registrierung oder Anmeldung</span><span class="sxs-lookup"><span data-stu-id="0020c-156">Sign-up/sign-in user flow</span></span>       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | <span data-ttu-id="0020c-157">Primäre, Herausgeber- oder Mandantendomäne</span><span class="sxs-lookup"><span data-stu-id="0020c-157">Primary/Publisher/Tenant domain</span></span> | `contoso.onmicrosoft.com`              |

<span data-ttu-id="0020c-158">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="0020c-158">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="0020c-159">Im Azure-Portal ist die App-Plattformkonfiguration **Umleitungs-URI** für Apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="0020c-159">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="0020c-160">Wenn die App auf einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der App im Panel **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="0020c-160">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="0020c-161">Wenn der Port nicht zuvor mit dem bekannten Port der App konfiguriert wurde, kehren Sie zur App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.</span><span class="sxs-lookup"><span data-stu-id="0020c-161">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="0020c-162">Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen durchführen können:</span><span class="sxs-lookup"><span data-stu-id="0020c-162">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="0020c-163">Anmelden bei der App mit einem AAD-Benutzerkonto</span><span class="sxs-lookup"><span data-stu-id="0020c-163">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="0020c-164">Anfordern von Zugriffstoken für Microsoft-APIs</span><span class="sxs-lookup"><span data-stu-id="0020c-164">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="0020c-165">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="0020c-165">For more information, see:</span></span>
  * [<span data-ttu-id="0020c-166">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="0020c-166">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="0020c-167">[Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="0020c-167">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="0020c-168">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="0020c-168">Authentication package</span></span>

<span data-ttu-id="0020c-169">Wenn eine App zum Verwenden eines B2C-Einzelkontos (`IndividualB2C`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span><span class="sxs-lookup"><span data-stu-id="0020c-169">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="0020c-170">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="0020c-170">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="0020c-171">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="0020c-171">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="0020c-172">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="0020c-172">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="0020c-173">Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket transitiv hinzu.</span><span class="sxs-lookup"><span data-stu-id="0020c-173">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="0020c-174">Unterstützung für Authentifizierungsdienste</span><span class="sxs-lookup"><span data-stu-id="0020c-174">Authentication service support</span></span>

<span data-ttu-id="0020c-175">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="0020c-175">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="0020c-176">Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.</span><span class="sxs-lookup"><span data-stu-id="0020c-176">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="0020c-177">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="0020c-177">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="0020c-178">Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter.</span><span class="sxs-lookup"><span data-stu-id="0020c-178">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="0020c-179">Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration beim Registrieren der App abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="0020c-179">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="0020c-180">Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="0020c-180">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="0020c-181">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0020c-181">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="0020c-182">Zugriffstokenbereiche</span><span class="sxs-lookup"><span data-stu-id="0020c-182">Access token scopes</span></span>

<span data-ttu-id="0020c-183">Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch so, dass diese ein Zugriffstoken für eine sichere API anfordert.</span><span class="sxs-lookup"><span data-stu-id="0020c-183">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="0020c-184">Zum Bereitstellen eines Zugriffstokens als Teil des Anmeldeflows fügen Sie den Bereich den Standard-Zugriffstokenbereichen von <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> hinzu:</span><span class="sxs-lookup"><span data-stu-id="0020c-184">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="0020c-185">Geben Sie zusätzliche Bereiche mit `AdditionalScopesToConsent` an:</span><span class="sxs-lookup"><span data-stu-id="0020c-185">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="0020c-186">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:</span><span class="sxs-lookup"><span data-stu-id="0020c-186">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="0020c-187">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="0020c-187">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="0020c-188">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="0020c-188">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="0020c-189">Anmeldemodus</span><span class="sxs-lookup"><span data-stu-id="0020c-189">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="0020c-190">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="0020c-190">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="0020c-191">Indexseite</span><span class="sxs-lookup"><span data-stu-id="0020c-191">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="0020c-192">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="0020c-192">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="0020c-193">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="0020c-193">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="0020c-194">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="0020c-194">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="0020c-195">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="0020c-195">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="0020c-196">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0020c-196">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="0020c-197">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="0020c-197">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="0020c-198">Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="0020c-198">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="0020c-199">Tutorial: Registrieren Sie eine Anwendung in Azure Active Directory B2C</span><span class="sxs-lookup"><span data-stu-id="0020c-199">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="0020c-200">Dokumentation zu Microsoft Identity Platform</span><span class="sxs-lookup"><span data-stu-id="0020c-200">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
