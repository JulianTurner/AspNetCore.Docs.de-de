---
title: Cloudauthentifizierung mit Azure Active Directory B2C in ASP.net Core
author: camsoper
description: Erfahren Sie, wie Sie Azure Active Directory B2C Authentifizierung mit ASP.net Core einrichten.
ms.author: casoper
ms.custom: devx-track-csharp, mvc
ms.date: 01/21/2019
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
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: f917bec8f2d929e62bf43494159a63458f135c5f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061391"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="be365-103">Cloudauthentifizierung mit Azure Active Directory B2C in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="be365-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="be365-104">Von [Cam Soper](https://twitter.com/camsoper)</span><span class="sxs-lookup"><span data-stu-id="be365-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="be365-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) ist eine Cloud-Identitäts Verwaltungs Lösung für Web-und Mobile Apps.</span><span class="sxs-lookup"><span data-stu-id="be365-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="be365-106">Der Dienst bietet eine Authentifizierung für apps, die in der Cloud und lokal gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="be365-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="be365-107">Zu den Authentifizierungs Typen zählen einzelne Konten, soziale Netzwerk Konten und Verbund Unternehmenskonten.</span><span class="sxs-lookup"><span data-stu-id="be365-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="be365-108">Darüber hinaus können Azure AD B2C Multi-Factor Authentication mit minimaler Konfiguration bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="be365-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="be365-109">Azure Active Directory (Azure AD) und Azure AD B2C sind separate Produktangebote.</span><span class="sxs-lookup"><span data-stu-id="be365-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="be365-110">Ein Azure AD Mandant stellt eine Organisation dar, während ein Azure AD B2C Mandant eine Auflistung von Identitäten darstellt, die mit Anwendungen der vertrauenden Seite verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="be365-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="be365-111">Weitere Informationen finden Sie unter [Azure AD B2C: häufig gestellte Fragen (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="be365-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="be365-112">In diesem Tutorial lernen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="be365-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="be365-113">Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="be365-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="be365-114">Registrieren einer APP in Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="be365-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="be365-115">Erstellen Sie mit Visual Studio eine ASP.net Core Web-App, die für die Verwendung des Azure AD B2C-Mandanten für die Authentifizierung konfiguriert ist</span><span class="sxs-lookup"><span data-stu-id="be365-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="be365-116">Konfigurieren von Richtlinien, die das Verhalten des Azure AD B2C Mandanten Steuern</span><span class="sxs-lookup"><span data-stu-id="be365-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="be365-117">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="be365-117">Prerequisites</span></span>

<span data-ttu-id="be365-118">Für diese exemplarische Vorgehensweise sind die folgenden Schritte erforderlich:</span><span class="sxs-lookup"><span data-stu-id="be365-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="be365-119">Microsoft Azure-Abonnement</span><span class="sxs-lookup"><span data-stu-id="be365-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="be365-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="be365-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="be365-121">Erstellen des Azure Active Directory B2C Mandanten</span><span class="sxs-lookup"><span data-stu-id="be365-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="be365-122">Erstellen Sie einen Azure Active Directory B2C Mandanten, [wie in der Dokumentation beschrieben](/azure/active-directory-b2c/active-directory-b2c-get-started).</span><span class="sxs-lookup"><span data-stu-id="be365-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="be365-123">Wenn Sie dazu aufgefordert werden, ist die Zuordnung des Mandanten zu einem Azure-Abonnement für dieses Tutorial optional.</span><span class="sxs-lookup"><span data-stu-id="be365-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="be365-124">Registrieren der app in Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="be365-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="be365-125">Registrieren Sie Ihre APP im neu erstellten Azure AD B2C Mandanten mithilfe [der Schritte in der Dokumentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) im Abschnitt **Registrieren einer Web-App** .</span><span class="sxs-lookup"><span data-stu-id="be365-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="be365-126">Legen Sie im Abschnitt **Erstellen eines geheimen Client Schlüssels für eine Web-App** an.</span><span class="sxs-lookup"><span data-stu-id="be365-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="be365-127">Ein geheimer Client Schlüssel ist für dieses Tutorial nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="be365-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="be365-128">Verwenden Sie die folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="be365-128">Use the following values:</span></span>

| <span data-ttu-id="be365-129">Einstellung</span><span class="sxs-lookup"><span data-stu-id="be365-129">Setting</span></span>                       | <span data-ttu-id="be365-130">Wert</span><span class="sxs-lookup"><span data-stu-id="be365-130">Value</span></span>                     | <span data-ttu-id="be365-131">Hinweise</span><span class="sxs-lookup"><span data-stu-id="be365-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="be365-132">**Name**</span><span class="sxs-lookup"><span data-stu-id="be365-132">**Name**</span></span>                      | <span data-ttu-id="be365-133">*&lt;App-Name&gt;*</span><span class="sxs-lookup"><span data-stu-id="be365-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="be365-134">Geben Sie einen **Namen** für die APP ein, mit dem Ihre APP für Consumer beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="be365-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="be365-135">**Web-App/Web-API einschließen**</span><span class="sxs-lookup"><span data-stu-id="be365-135">**Include web app / web API**</span></span> | <span data-ttu-id="be365-136">Yes</span><span class="sxs-lookup"><span data-stu-id="be365-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="be365-137">**Impliziten Fluss zulassen**</span><span class="sxs-lookup"><span data-stu-id="be365-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="be365-138">Yes</span><span class="sxs-lookup"><span data-stu-id="be365-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="be365-139">**Antwort-URL**</span><span class="sxs-lookup"><span data-stu-id="be365-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="be365-140">Antwort-URLs sind Endpunkte, an denen Azure AD B2C von Ihrer App angeforderte Token zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="be365-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="be365-141">Visual Studio stellt die zu verwendende Antwort-URL bereit.</span><span class="sxs-lookup"><span data-stu-id="be365-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="be365-142">Geben Sie vorerst ein, `https://localhost:44300/signin-oidc` um das Formular abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="be365-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="be365-143">**App-ID-URI**</span><span class="sxs-lookup"><span data-stu-id="be365-143">**App ID URI**</span></span>                | <span data-ttu-id="be365-144">Nicht ausfüllen</span><span class="sxs-lookup"><span data-stu-id="be365-144">Leave blank</span></span>               | <span data-ttu-id="be365-145">Für dieses Tutorial nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="be365-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="be365-146">**Nativen Client einschließen**</span><span class="sxs-lookup"><span data-stu-id="be365-146">**Include native client**</span></span>     | <span data-ttu-id="be365-147">No</span><span class="sxs-lookup"><span data-stu-id="be365-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="be365-148">Wenn Sie eine nicht-localhost-Antwort-URL einrichten, beachten Sie die Einschränkungen, die [in der Antwort-URL-Liste zulässig sind](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span><span class="sxs-lookup"><span data-stu-id="be365-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="be365-149">Nachdem die APP registriert wurde, wird die Liste der apps im Mandanten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="be365-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="be365-150">Wählen Sie die soeben registrierte App aus.</span><span class="sxs-lookup"><span data-stu-id="be365-150">Select the app that was just registered.</span></span> <span data-ttu-id="be365-151">Wählen Sie das **Kopier** Symbol rechts neben dem Feld **Anwendungs-ID** aus, um es in die Zwischenablage zu kopieren.</span><span class="sxs-lookup"><span data-stu-id="be365-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="be365-152">Im Azure AD B2C Mandanten kann zurzeit nichts mehr konfiguriert werden, aber lassen Sie das Browserfenster geöffnet.</span><span class="sxs-lookup"><span data-stu-id="be365-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="be365-153">Nachdem die ASP.net Core-App erstellt wurde, ist eine weitere Konfiguration vorhanden.</span><span class="sxs-lookup"><span data-stu-id="be365-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="be365-154">Erstellen einer ASP.net Core-app in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="be365-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="be365-155">Die Visual Studio-Webanwendungs Vorlage kann so konfiguriert werden, dass der Azure AD B2C Mandanten für die Authentifizierung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="be365-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="be365-156">In Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="be365-156">In Visual Studio:</span></span>

1. <span data-ttu-id="be365-157">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="be365-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="be365-158">Wählen Sie in der Liste der Vorlagen die Option **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="be365-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="be365-159">Wählen Sie die Schaltfläche **Authentifizierung ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="be365-159">Select the **Change Authentication** button.</span></span>
    
    ![Authentifizierungs Schaltfläche ändern](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="be365-161">Wählen Sie im Dialogfeld **Authentifizierung ändern** die Option **einzelne Benutzerkonten** aus, und wählen Sie dann in der Dropdown Liste die **Option mit einem vorhandenen Benutzerspeicher in der Cloud verbinden** aus.</span><span class="sxs-lookup"><span data-stu-id="be365-161">In the **Change Authentication** dialog, select **Individual User Accounts** , and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Dialog Feld zur Authentifizierung ändern](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="be365-163">Füllen Sie das Formular mit den folgenden Werten aus:</span><span class="sxs-lookup"><span data-stu-id="be365-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="be365-164">Einstellung</span><span class="sxs-lookup"><span data-stu-id="be365-164">Setting</span></span>                       | <span data-ttu-id="be365-165">Wert</span><span class="sxs-lookup"><span data-stu-id="be365-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="be365-166">**Domänen Name**</span><span class="sxs-lookup"><span data-stu-id="be365-166">**Domain Name**</span></span>               | <span data-ttu-id="be365-167">*&lt;der Domänen Name Ihres B2C-Mandanten.&gt;*</span><span class="sxs-lookup"><span data-stu-id="be365-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="be365-168">**Anwendungs-ID**</span><span class="sxs-lookup"><span data-stu-id="be365-168">**Application ID**</span></span>            | <span data-ttu-id="be365-169">*&lt;Fügen Sie die Anwendungs-ID aus der Zwischenablage ein.&gt;*</span><span class="sxs-lookup"><span data-stu-id="be365-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="be365-170">**Rückruf Pfad**</span><span class="sxs-lookup"><span data-stu-id="be365-170">**Callback Path**</span></span>             | <span data-ttu-id="be365-171">*&lt;Standardwert verwenden&gt;*</span><span class="sxs-lookup"><span data-stu-id="be365-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="be365-172">**Registrierungs-oder Anmelde Richtlinie**</span><span class="sxs-lookup"><span data-stu-id="be365-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="be365-173">**Kenn Wort Richtlinie zurücksetzen**</span><span class="sxs-lookup"><span data-stu-id="be365-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="be365-174">**Profil Richtlinie bearbeiten**</span><span class="sxs-lookup"><span data-stu-id="be365-174">**Edit profile policy**</span></span>       | <span data-ttu-id="be365-175">*&lt;leer lassen&gt;*</span><span class="sxs-lookup"><span data-stu-id="be365-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="be365-176">Wählen Sie den Link **Kopieren** neben dem **Antwort-URI** aus, um den Antwort-URI in die Zwischenablage zu kopieren.</span><span class="sxs-lookup"><span data-stu-id="be365-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="be365-177">Wählen Sie **OK** aus, um das Dialogfeld **Authentifizierung ändern** zu schließen.</span><span class="sxs-lookup"><span data-stu-id="be365-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="be365-178">Wählen Sie **OK** , um die Web-App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="be365-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="be365-179">Abschließen der Registrierung der B2C-App</span><span class="sxs-lookup"><span data-stu-id="be365-179">Finish the B2C app registration</span></span>

<span data-ttu-id="be365-180">Kehren Sie zum Browserfenster zurück, in dem die B2C-App-Eigenschaften noch geöffnet sind.</span><span class="sxs-lookup"><span data-stu-id="be365-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="be365-181">Ändern Sie die zuvor angegebene temporäre **Antwort-URL** in den aus Visual Studio kopierten Wert.</span><span class="sxs-lookup"><span data-stu-id="be365-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="be365-182">Wählen Sie am oberen Rand des Fensters die Option **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="be365-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="be365-183">Wenn Sie die Antwort-URL nicht kopiert haben, verwenden Sie die HTTPS-Adresse auf der Registerkarte "Debuggen" in den Webprojekt Eigenschaften, und fügen Sie den **callbackpath** -Wert von an *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="be365-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *:::no-loc(appsettings.json):::* .</span></span>

## <a name="configure-policies"></a><span data-ttu-id="be365-184">Konfigurieren von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="be365-184">Configure policies</span></span>

<span data-ttu-id="be365-185">Verwenden Sie die Schritte in der Azure AD B2C-Dokumentation, um [eine Registrierungs-oder Anmelde Richtlinie zu erstellen](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), und [Erstellen Sie dann eine Richtlinie zum Zurücksetzen des Kennworts](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="be365-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="be365-186">Verwenden Sie die Beispiel Werte, die in der Dokumentation für **:::no-loc(Identity)::: Anbieter** , Registrierungs **Attribute** und **Anwendungs Ansprüche** bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="be365-186">Use the example values provided in the documentation for **:::no-loc(Identity)::: providers** , **Sign-up attributes** , and **Application claims** .</span></span> <span data-ttu-id="be365-187">Die Verwendung der Schaltfläche **jetzt ausführen** zum Testen der Richtlinien, wie in der Dokumentation beschrieben, ist optional.</span><span class="sxs-lookup"><span data-stu-id="be365-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="be365-188">Stellen Sie sicher, dass die Richtlinien Namen genau wie in der Dokumentation beschrieben sind, da diese Richtlinien im Dialogfeld " **Authentifizierung ändern** " in Visual Studio verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="be365-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="be365-189">Die Richtlinien Namen können in überprüft werden *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="be365-189">The policy names can be verified in *:::no-loc(appsettings.json):::* .</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearerno-loccookie-options"></a><span data-ttu-id="be365-190">Konfigurieren der zugrunde liegenden openidconnectoptions/jwtbearer- :::no-loc(Cookie)::: Optionen</span><span class="sxs-lookup"><span data-stu-id="be365-190">Configure the underlying OpenIdConnectOptions/JwtBearer/:::no-loc(Cookie)::: options</span></span>

<span data-ttu-id="be365-191">Um die zugrunde liegenden Optionen direkt zu konfigurieren, verwenden Sie die entsprechende Schema Konstante in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="be365-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<:::no-loc(Cookie):::AuthenticationOptions>(
    AzureAD[B2C]Defaults.:::no-loc(Cookie):::Scheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="be365-192">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="be365-192">Run the app</span></span>

<span data-ttu-id="be365-193">Drücken Sie in Visual Studio **F5** , um die APP zu erstellen und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="be365-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="be365-194">Wählen Sie nach dem Start der Web-App **akzeptieren** aus, um die Verwendung von s zu akzeptieren :::no-loc(cookie)::: (falls Sie dazu aufgefordert werden), und wählen Sie dann **Anmelden** aus.</span><span class="sxs-lookup"><span data-stu-id="be365-194">After the web app launches, select **Accept** to accept the use of :::no-loc(cookie):::s (if prompted), and then select **Sign in** .</span></span>

![Anmelden bei der APP](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="be365-196">Der Browser leitet an den Azure AD B2C-Mandanten weiter.</span><span class="sxs-lookup"><span data-stu-id="be365-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="be365-197">Melden Sie sich mit einem vorhandenen Konto an (sofern erstellt wurde, testen Sie die Richtlinien), oder wählen Sie **jetzt registrieren** aus, um ein neues Konto zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="be365-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="be365-198">Der Link " **Kennwort vergessen?** " wird verwendet, um ein vergessenes Kennwort zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="be365-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Azure AD B2C-Anmeldung](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="be365-200">Nach erfolgreicher Anmeldung wird der Browser an die Web-App umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="be365-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Erfolg](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="be365-202">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="be365-202">Next steps</span></span>

<span data-ttu-id="be365-203">In diesem Tutorial haben Sie Folgendes gelernt:</span><span class="sxs-lookup"><span data-stu-id="be365-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="be365-204">Erstellen eines Azure Active Directory B2C-Mandanten</span><span class="sxs-lookup"><span data-stu-id="be365-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="be365-205">Registrieren einer APP in Azure AD B2C</span><span class="sxs-lookup"><span data-stu-id="be365-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="be365-206">Verwenden von Visual Studio zum Erstellen einer ASP.net Core-Webanwendung, die für die Verwendung des Azure AD B2C-Mandanten zur Authentifizierung konfiguriert</span><span class="sxs-lookup"><span data-stu-id="be365-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="be365-207">Konfigurieren von Richtlinien, die das Verhalten des Azure AD B2C Mandanten Steuern</span><span class="sxs-lookup"><span data-stu-id="be365-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="be365-208">Nun, da die ASP.net Core-App für die Verwendung von Azure AD B2C für die Authentifizierung konfiguriert ist, kann das [Autorisierungs Attribut](xref:security/authorization/simple) verwendet werden, um Ihre APP zu sichern.</span><span class="sxs-lookup"><span data-stu-id="be365-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="be365-209">Entwickeln Sie Ihre APP weiter, indem Sie Folgendes lernen:</span><span class="sxs-lookup"><span data-stu-id="be365-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="be365-210">[Passen Sie die Azure AD B2C Benutzeroberfläche](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization)an.</span><span class="sxs-lookup"><span data-stu-id="be365-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="be365-211">Konfigurieren von Anforderungen für die Kenn [Wort Komplexität](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)</span><span class="sxs-lookup"><span data-stu-id="be365-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="be365-212">[Aktivieren Sie Multi-Factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="be365-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="be365-213">Konfigurieren Sie zusätzliche Identitäts Anbieter wie [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)und andere.</span><span class="sxs-lookup"><span data-stu-id="be365-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="be365-214">[Verwenden Sie die Azure AD Graph-API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) , um zusätzliche Benutzerinformationen, wie z. b. die Gruppenmitgliedschaft, aus dem Azure AD B2C-Mandanten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="be365-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="be365-215">[So sichern Sie eine Web-API, die mit ASP.net Core erstellt wurde, mithilfe des Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span><span class="sxs-lookup"><span data-stu-id="be365-215">[How to secure a Web API built with ASP.NET Core using the Azure AD B2C](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C).</span></span>
* <span data-ttu-id="be365-216">[Tutorial: Gewähren des Zugriffs auf eine ASP.net-Web-API mithilfe von Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="be365-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>
