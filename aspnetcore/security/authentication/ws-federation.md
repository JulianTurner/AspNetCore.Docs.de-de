---
title: Authentifizieren von Benutzern mit WS-Federation in ASP.net Core
author: chlowell
description: In diesem Tutorial wird veranschaulicht, wie WS-Federation in einer ASP.net Core-App verwendet wird.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
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
uid: security/authentication/ws-federation
ms.openlocfilehash: ed78923a2bdd1ed683a72c0a6f34337a38350035
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053370"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="fa88a-103">Authentifizieren von Benutzern mit WS-Federation in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="fa88a-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="fa88a-104">In diesem Tutorial wird veranschaulicht, wie Sie es Benutzern ermöglichen, sich mit einem WS-Federation Authentifizierungs Anbieter wie Active Directory-Verbunddienste (AD FS) (ADFS) oder [Azure Active Directory](/azure/active-directory/) (AAD) anzumelden.</span><span class="sxs-lookup"><span data-stu-id="fa88a-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="fa88a-105">Dabei wird die ASP.net Core-Beispiel-App verwendet, die in [Facebook, Google und der Authentifizierung externer Anbieter beschrieben wird](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="fa88a-105">It uses the ASP.NET Core sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="fa88a-106">Für ASP.net Core-apps wird WS-Federation Support von [Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="fa88a-106">For ASP.NET Core apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="fa88a-107">Diese Komponente wird von [Microsoft. owin. Security. wsfederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) portiert und nutzt viele der Mechanismen dieser Komponente.</span><span class="sxs-lookup"><span data-stu-id="fa88a-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="fa88a-108">Allerdings unterscheiden sich die Komponenten auf einige wichtige Weise.</span><span class="sxs-lookup"><span data-stu-id="fa88a-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="fa88a-109">Standardmäßig wird die neue Middleware:</span><span class="sxs-lookup"><span data-stu-id="fa88a-109">By default, the new middleware:</span></span>

* <span data-ttu-id="fa88a-110">Lässt keine nicht angeforderten Anmeldungen zu.</span><span class="sxs-lookup"><span data-stu-id="fa88a-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="fa88a-111">Diese Funktion des WS-Federation Protokolls ist anfällig für XSRF-Angriffe.</span><span class="sxs-lookup"><span data-stu-id="fa88a-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="fa88a-112">Sie kann jedoch mit der Option aktiviert werden `AllowUnsolicitedLogins` .</span><span class="sxs-lookup"><span data-stu-id="fa88a-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="fa88a-113">Prüft nicht alle Formular Beiträge auf Anmelde Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="fa88a-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="fa88a-114">Nur Anforderungen an werden auf Anmeldungen `CallbackPath` geprüft. der Standardwert ist `CallbackPath` , `/signin-wsfed` aber kann über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [wsfederationoptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) -Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="fa88a-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="fa88a-115">Dieser Pfad kann für andere Authentifizierungs Anbieter freigegeben werden, indem die [skipunerkenzedrequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) -Option aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="fa88a-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="fa88a-116">Registrieren der App bei Active Directory</span><span class="sxs-lookup"><span data-stu-id="fa88a-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="fa88a-117">Active Directory-Verbunddienste (AD FS)</span><span class="sxs-lookup"><span data-stu-id="fa88a-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="fa88a-118">Öffnen **Sie den Assistenten zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite** in der AD FS-Verwaltungskonsole:</span><span class="sxs-lookup"><span data-stu-id="fa88a-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="fa88a-120">Daten manuell eingeben:</span><span class="sxs-lookup"><span data-stu-id="fa88a-120">Choose to enter data manually:</span></span>

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite: Datenquelle](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="fa88a-122">Geben Sie einen anzeigen Amen für die vertrauende Seite ein.</span><span class="sxs-lookup"><span data-stu-id="fa88a-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="fa88a-123">Der Name ist für die ASP.net Core-APP nicht wichtig.</span><span class="sxs-lookup"><span data-stu-id="fa88a-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="fa88a-124">[Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) hat keine Unterstützung für die tokenverschlüsselung, konfigurieren Sie also kein tokenverschlüsselungszertifikat:</span><span class="sxs-lookup"><span data-stu-id="fa88a-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="fa88a-126">Aktivieren Sie die Unterstützung für WS-Federation passives Protokoll mithilfe der App-URL.</span><span class="sxs-lookup"><span data-stu-id="fa88a-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="fa88a-127">Vergewissern Sie sich, dass der Port für die APP richtig ist:</span><span class="sxs-lookup"><span data-stu-id="fa88a-127">Verify the port is correct for the app:</span></span>

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="fa88a-129">Dabei muss es sich um eine HTTPS-URL handeln.</span><span class="sxs-lookup"><span data-stu-id="fa88a-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="fa88a-130">IIS Express können ein selbst signiertes Zertifikat bereitstellen, wenn die APP während der Entwicklung gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="fa88a-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="fa88a-131">Kestrel erfordert eine manuelle Zertifikat Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="fa88a-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="fa88a-132">Weitere Informationen finden Sie in der [Kestrel-Dokumentation](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="fa88a-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="fa88a-133">Klicken Sie im restlichen Assistenten auf **weiter** , und **Schließen** Sie am Ende.</span><span class="sxs-lookup"><span data-stu-id="fa88a-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="fa88a-134">:::no-loc(ASP.NET Core Identity)::: erfordert einen **namens-ID** -Anspruch.</span><span class="sxs-lookup"><span data-stu-id="fa88a-134">:::no-loc(ASP.NET Core Identity)::: requires a **Name ID** claim.</span></span> <span data-ttu-id="fa88a-135">Fügen Sie eine aus dem Dialogfeld " **Anspruchs Regeln bearbeiten** " hinzu:</span><span class="sxs-lookup"><span data-stu-id="fa88a-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Anspruchsregeln bearbeiten](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="fa88a-137">Belassen Sie im **Assistenten zum Hinzufügen von Transformations Anspruchs Regeln** die Vorlage Standardwert **für LDAP-Attribute als Ansprüche senden** , und klicken Sie auf **weiter** .</span><span class="sxs-lookup"><span data-stu-id="fa88a-137">In the **Add Transform Claim Rule Wizard** , leave the default **Send LDAP Attributes as Claims** template selected, and click **Next** .</span></span> <span data-ttu-id="fa88a-138">Fügen Sie eine Regel hinzu, die das LDAP **-Attribut Sam-Account-Name** dem ausgehenden Anspruch der **namens-ID** zuordnet:</span><span class="sxs-lookup"><span data-stu-id="fa88a-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Assistent zum Hinzufügen von Transformations Anspruchs Regeln: Konfigurieren von Anspruchs Regeln](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="fa88a-140">Klicken **Finish** Sie  >  **OK** im Fenster **Anspruchs Regeln bearbeiten** auf Fertigstellen.</span><span class="sxs-lookup"><span data-stu-id="fa88a-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="fa88a-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="fa88a-141">Azure Active Directory</span></span>

* <span data-ttu-id="fa88a-142">Navigieren Sie zum Blatt App-Registrierungen des Aad-Mandanten.</span><span class="sxs-lookup"><span data-stu-id="fa88a-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="fa88a-143">Klicken Sie auf **neue Anwendungs Registrierung** :</span><span class="sxs-lookup"><span data-stu-id="fa88a-143">Click **New application registration** :</span></span>

![Azure Active Directory: App-Registrierungen](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="fa88a-145">Geben Sie einen Namen für die APP-Registrierung ein.</span><span class="sxs-lookup"><span data-stu-id="fa88a-145">Enter a name for the app registration.</span></span> <span data-ttu-id="fa88a-146">Dies ist für die ASP.net Core-APP nicht von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="fa88a-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="fa88a-147">Geben Sie die URL ein, die die APP als **Anmelde-URL** überwacht:</span><span class="sxs-lookup"><span data-stu-id="fa88a-147">Enter the URL the app listens on as the **Sign-on URL** :</span></span>

![Azure Active Directory: App-Registrierung erstellen](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="fa88a-149">Klicken Sie auf **Endpunkte** , und notieren Sie die Verbund **Metadaten-Dokument** -URL</span><span class="sxs-lookup"><span data-stu-id="fa88a-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="fa88a-150">Dies sind die WS-Federation Middleware `MetadataAddress` :</span><span class="sxs-lookup"><span data-stu-id="fa88a-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: Endpunkte](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="fa88a-152">Navigieren Sie zur neuen App-Registrierung.</span><span class="sxs-lookup"><span data-stu-id="fa88a-152">Navigate to the new app registration.</span></span> <span data-ttu-id="fa88a-153">Klicken Sie auf **API** verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="fa88a-153">Click **Expose an API** .</span></span> <span data-ttu-id="fa88a-154">Klicken Sie auf Anwendungs-ID-URI **Satz**  >  **Speichern** .</span><span class="sxs-lookup"><span data-stu-id="fa88a-154">Click Application ID URI **Set** > **Save** .</span></span> <span data-ttu-id="fa88a-155">Notieren Sie sich den  **Anwendungs-ID-URI** .</span><span class="sxs-lookup"><span data-stu-id="fa88a-155">Make note of the  **Application ID URI** .</span></span> <span data-ttu-id="fa88a-156">Dies sind die WS-Federation Middleware `Wtrealm` :</span><span class="sxs-lookup"><span data-stu-id="fa88a-156">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: Eigenschaften der APP-Registrierung](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-no-locaspnet-core-identity"></a><span data-ttu-id="fa88a-158">Verwenden Sie WS-Federation ohne :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="fa88a-158">Use WS-Federation without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="fa88a-159">Die WS-Federation Middleware kann ohne verwendet werden :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="fa88a-159">The WS-Federation middleware can be used without :::no-loc(Identity):::.</span></span> <span data-ttu-id="fa88a-160">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fa88a-160">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-no-locaspnet-core-identity"></a><span data-ttu-id="fa88a-161">Fügen Sie WS-Federation als externen Anmelde Anbieter für hinzu. :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="fa88a-161">Add WS-Federation as an external login provider for :::no-loc(ASP.NET Core Identity):::</span></span>

* <span data-ttu-id="fa88a-162">Fügen Sie eine Abhängigkeit von [Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa88a-162">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="fa88a-163">WS-Federation hinzufügen zu `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="fa88a-163">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="fa88a-164">Melden Sie sich mit WS-Federation</span><span class="sxs-lookup"><span data-stu-id="fa88a-164">Log in with WS-Federation</span></span>

<span data-ttu-id="fa88a-165">Navigieren Sie zur APP, und klicken Sie auf den Link **Anmelden** im Navigations Header.</span><span class="sxs-lookup"><span data-stu-id="fa88a-165">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="fa88a-166">Es gibt eine Option zum Anmelden mit wsfederation: ![ Anmeldeseite.](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="fa88a-166">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="fa88a-167">Bei ADFS als Anbieter wird die Schaltfläche zu einer AD FS-Anmeldeseite umgeleitet: ![ ADFS-Anmeldeseite](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="fa88a-167">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="fa88a-168">Wenn Azure Active Directory als Anbieter fungiert, wird die Schaltfläche an eine Aad-Anmeldeseite umgeleitet: ![ Aad-Anmeldeseite](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="fa88a-168">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="fa88a-169">Eine erfolgreiche Anmeldung für einen neuen Benutzer leitet zur Benutzer Registrierungsseite der App um: ![ Seite registrieren.](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="fa88a-169">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>
