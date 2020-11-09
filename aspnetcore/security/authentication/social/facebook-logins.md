---
title: Einrichtung externer Facebook-Anmelde Informationen in ASP.net Core
author: rick-anderson
description: Tutorial mit Codebeispielen, die die Integration der Authentifizierung von Facebook-Konto Benutzern in eine vorhandene ASP.net Core-App veranschaulichen.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: be0b655645fd2bd0eab9f9c30a65485f386cead3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053357"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="0ec41-103">Einrichtung externer Facebook-Anmelde Informationen in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="0ec41-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="0ec41-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0ec41-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="0ec41-105">In diesem Tutorial mit Codebeispielen wird veranschaulicht, wie Sie es Ihren Benutzern ermöglichen können, sich mit Ihrem Facebook-Konto zu anmelden, indem Sie ein Beispiel ASP.net Core auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiel Projekt 3,0.</span><span class="sxs-lookup"><span data-stu-id="0ec41-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="0ec41-106">Wir beginnen damit, eine Facebook-APP-ID zu erstellen, indem wir die [offiziellen Schritte](https://developers.facebook.com)befolgen.</span><span class="sxs-lookup"><span data-stu-id="0ec41-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="0ec41-107">Erstellen der app in Facebook</span><span class="sxs-lookup"><span data-stu-id="0ec41-107">Create the app in Facebook</span></span>

* <span data-ttu-id="0ec41-108">Fügen Sie das nuget-Paket [Microsoft. aspnetcore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="0ec41-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="0ec41-109">Navigieren Sie zur Seite [Facebook-Entwickler-App](https://developers.facebook.com/apps/) , und melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="0ec41-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="0ec41-110">Wenn Sie noch nicht über ein Facebook-Konto verfügen, verwenden Sie den Link **für Facebook registrieren** auf der Anmeldeseite, um einen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0ec41-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="0ec41-111">Wenn Sie über ein Facebook-Konto verfügen, befolgen Sie die Anweisungen, um sich als Facebook-Entwickler zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="0ec41-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="0ec41-112">Wählen Sie im Menü **meine apps** die Option **app erstellen** aus, um eine neue APP-ID zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0ec41-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Facebook für Entwickler Portal in Microsoft Edge geöffnet](index/_static/FBMyApps.png)

* <span data-ttu-id="0ec41-114">Füllen Sie das Formular aus, und tippen Sie auf die Schaltfläche **App-ID erstellen** .</span><span class="sxs-lookup"><span data-stu-id="0ec41-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Erstellen Sie ein neues APP-ID-Formular.](index/_static/FBNewAppId.png)

* <span data-ttu-id="0ec41-116">Wählen Sie auf der neuen App-Karte **Produkt hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="0ec41-116">On the new App card, select **Add a Product** .</span></span>  <span data-ttu-id="0ec41-117">Klicken Sie auf der **Facebook-Anmelde** Karte auf **Einrichten** .</span><span class="sxs-lookup"><span data-stu-id="0ec41-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Seite "Produkt Setup"](index/_static/FBProductSetup.png)

* <span data-ttu-id="0ec41-119">Der **Schnellstart** -Assistent wird gestartet, und **Wählen Sie eine Plattform** als erste Seite aus.</span><span class="sxs-lookup"><span data-stu-id="0ec41-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="0ec41-120">Umgehen Sie den Assistenten, indem Sie im Menü unten links auf den Link **Facebook Login** **Settings** (Facebook-Anmelde Einstellungen) klicken:</span><span class="sxs-lookup"><span data-stu-id="0ec41-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Schnellstart überspringen](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="0ec41-122">Die Seite "Client- **OAuth-Einstellungen** " wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0ec41-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![Client-OAuth-Einstellungsseite](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="0ec41-124">Geben Sie Ihren Entwicklungs-URI mit */SignIn-Facebook* an das **gültige OAuth-Umleitungs-URIs** -Feld an (Beispiel: `https://localhost:44320/signin-facebook` ).</span><span class="sxs-lookup"><span data-stu-id="0ec41-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="0ec41-125">Bei der Facebook-Authentifizierung, die später in diesem Tutorial konfiguriert wird, werden Anforderungen an */SignIn-Facebook* Route automatisch verarbeitet, um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="0ec41-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="0ec41-126">Der URI */SignIn-Facebook* wird als Standard Rückruf des Facebook-Authentifizierungs Anbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="0ec41-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="0ec41-127">Sie können den Standard-Rückruf-URI beim Konfigurieren der Facebook-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [facebookoptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) -Klasse ändern.</span><span class="sxs-lookup"><span data-stu-id="0ec41-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="0ec41-128">Klicken Sie auf **Änderungen speichern** .</span><span class="sxs-lookup"><span data-stu-id="0ec41-128">Click **Save Changes** .</span></span>

* <span data-ttu-id="0ec41-129">Klicken Sie im linken Navigationsbereich auf **Einstellungen**  >  **Basic** -Link.</span><span class="sxs-lookup"><span data-stu-id="0ec41-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="0ec41-130">Notieren Sie sich auf dieser Seite Ihren `App ID` und ihren `App Secret` .</span><span class="sxs-lookup"><span data-stu-id="0ec41-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="0ec41-131">Sie fügen beide im nächsten Abschnitt Ihrer ASP.net Core Anwendung hinzu:</span><span class="sxs-lookup"><span data-stu-id="0ec41-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="0ec41-132">Wenn Sie den Standort bereitstellen, müssen Sie die Seite für die **Facebook-Anmelde** Einrichtung erneut aufrufen und einen neuen öffentlichen URI registrieren.</span><span class="sxs-lookup"><span data-stu-id="0ec41-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="0ec41-133">Speichern der Facebook-APP-ID und des geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="0ec41-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="0ec41-134">Speichern Sie sensible Einstellungen wie die Facebook-APP-ID und die geheimen Werte mit dem [Geheimnis-Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="0ec41-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="0ec41-135">Führen Sie für dieses Beispiel die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="0ec41-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="0ec41-136">Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Aktivieren der geheimen Speicherung](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="0ec41-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="0ec41-137">Speichern Sie die sensiblen Einstellungen im lokalen geheimen Speicher mit den geheimen Schlüsseln `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` :</span><span class="sxs-lookup"><span data-stu-id="0ec41-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="0ec41-138">Konfigurieren der Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="0ec41-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="0ec41-139">Fügen Sie den Facebook-Dienst in der- `ConfigureServices` Methode in der *Startup.cs* -Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="0ec41-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="0ec41-140">Mit Facebook anmelden</span><span class="sxs-lookup"><span data-stu-id="0ec41-140">Sign in with Facebook</span></span>

* <span data-ttu-id="0ec41-141">Führen Sie die APP aus, und wählen Sie **Anmelden aus** .</span><span class="sxs-lookup"><span data-stu-id="0ec41-141">Run the app and select **Log in** .</span></span> 
* <span data-ttu-id="0ec41-142">Wählen Sie unter **anderen Dienst zum Anmelden verwenden. die** Option Facebook aus.</span><span class="sxs-lookup"><span data-stu-id="0ec41-142">Under **Use another service to log in.** , select Facebook.</span></span>
* <span data-ttu-id="0ec41-143">Sie werden zur Authentifizierung an **Facebook** umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="0ec41-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="0ec41-144">Ihre Facebook-Anmelde Informationen eingeben.</span><span class="sxs-lookup"><span data-stu-id="0ec41-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="0ec41-145">Sie werden zurück zu Ihrer Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.</span><span class="sxs-lookup"><span data-stu-id="0ec41-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="0ec41-146">Sie sind jetzt mit ihren Facebook-Anmelde Informationen angemeldet:</span><span class="sxs-lookup"><span data-stu-id="0ec41-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="0ec41-147">Reagieren auf Abbrechen der externen Anmeldung</span><span class="sxs-lookup"><span data-stu-id="0ec41-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="0ec41-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> kann einen Umleitungs Pfad zum Benutzer-Agent angeben, wenn der Benutzer die angeforderte Autorisierungs Anforderung nicht genehmigt.</span><span class="sxs-lookup"><span data-stu-id="0ec41-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="0ec41-149">Mit dem folgenden Code wird der auf festgelegt `AccessDeniedPath` `"/AccessDeniedPathInfo"` :</span><span class="sxs-lookup"><span data-stu-id="0ec41-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="0ec41-150">Wir empfehlen, dass die `AccessDeniedPath` Seite die folgenden Informationen enthält:</span><span class="sxs-lookup"><span data-stu-id="0ec41-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="0ec41-151">Die Remote Authentifizierung wurde abgebrochen.</span><span class="sxs-lookup"><span data-stu-id="0ec41-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="0ec41-152">Diese APP erfordert eine Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="0ec41-152">This app requires authentication.</span></span>
* <span data-ttu-id="0ec41-153">Wählen Sie den Anmeldelink aus, um erneut zu versuchen, sich anzumelden.</span><span class="sxs-lookup"><span data-stu-id="0ec41-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="0ec41-154">Testen von accessdeniedpath</span><span class="sxs-lookup"><span data-stu-id="0ec41-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="0ec41-155">Navigieren Sie zu [Facebook.com](https://www.facebook.com/)</span><span class="sxs-lookup"><span data-stu-id="0ec41-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="0ec41-156">Wenn Sie angemeldet sind, müssen Sie sich abmelden.</span><span class="sxs-lookup"><span data-stu-id="0ec41-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="0ec41-157">Führen Sie die APP aus, und wählen Sie die Facebook-Anmeldung aus.</span><span class="sxs-lookup"><span data-stu-id="0ec41-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="0ec41-158">Wählen Sie **nicht jetzt** aus.</span><span class="sxs-lookup"><span data-stu-id="0ec41-158">Select **Not now** .</span></span> <span data-ttu-id="0ec41-159">Sie werden auf die angegebene `AccessDeniedPath` Seite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="0ec41-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="0ec41-160">Weitere Informationen zu den von der Facebook-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [facebookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) -API-Referenz.</span><span class="sxs-lookup"><span data-stu-id="0ec41-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="0ec41-161">Konfigurationsoptionen können für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="0ec41-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="0ec41-162">Anfordern von anderen Informationen über den Benutzer.</span><span class="sxs-lookup"><span data-stu-id="0ec41-162">Request different information about the user.</span></span>
* <span data-ttu-id="0ec41-163">Fügen Sie Abfrage Zeichen folgen Argumente hinzu, um die Anmeldung anzupassen.</span><span class="sxs-lookup"><span data-stu-id="0ec41-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="0ec41-164">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="0ec41-164">Troubleshooting</span></span>

* <span data-ttu-id="0ec41-165">**Nur ASP.net Core 2. x:** Wenn Identity nicht durch Aufrufen von `services.AddIdentity` konfiguriert `ConfigureServices` wird, führt der Versuch, sich zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden* .</span><span class="sxs-lookup"><span data-stu-id="0ec41-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="0ec41-166">Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.</span><span class="sxs-lookup"><span data-stu-id="0ec41-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="0ec41-167">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="0ec41-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="0ec41-168">Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="0ec41-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0ec41-169">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="0ec41-169">Next steps</span></span>

* <span data-ttu-id="0ec41-170">In diesem Artikel wurde gezeigt, wie Sie sich mit Facebook authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="0ec41-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="0ec41-171">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="0ec41-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="0ec41-172">Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `AppSecret` im Facebook-Entwickler Portal zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="0ec41-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="0ec41-173">Legen Sie die `Authentication:Facebook:AppId` und `Authentication:Facebook:AppSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="0ec41-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="0ec41-174">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="0ec41-174">The configuration system is set up to read keys from environment variables.</span></span>
