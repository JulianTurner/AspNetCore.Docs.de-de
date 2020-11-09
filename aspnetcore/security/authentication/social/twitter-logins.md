---
title: Externe Twitter-Anmelde Einrichtung mit ASP.net Core
author: rick-anderson
description: In diesem Tutorial wird die Integration der Authentifizierung von Twitter-Konto Benutzern in eine vorhandene ASP.net Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: 47926d12ac5f922f2937df164d38ff6eb63cacf1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053279"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="19017-103">Externe Twitter-Anmelde Einrichtung mit ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="19017-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="19017-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="19017-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="19017-105">Dieses Beispiel zeigt, wie Sie es Benutzern mithilfe eines auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiels ASP.net Core 3,0-Projekts ermöglichen, sich [mit Ihrem Twitter-Konto anzumelden](https://dev.twitter.com/web/sign-in/desktop-browser) .</span><span class="sxs-lookup"><span data-stu-id="19017-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="19017-106">Erstellen der app in Twitter</span><span class="sxs-lookup"><span data-stu-id="19017-106">Create the app in Twitter</span></span>

* <span data-ttu-id="19017-107">Fügen Sie dem Projekt das nuget-Paket [Microsoft. aspnetcore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) hinzu.</span><span class="sxs-lookup"><span data-stu-id="19017-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="19017-108">Navigieren Sie zu, [https://apps.twitter.com/](https://apps.twitter.com/) und melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="19017-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="19017-109">Wenn Sie noch nicht über ein Twitter-Konto verfügen, verwenden Sie den Link **[jetzt registrieren](https://twitter.com/signup)** , um einen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="19017-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="19017-110">Wählen Sie **app erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="19017-110">Select **Create an app** .</span></span> <span data-ttu-id="19017-111">Geben Sie den **APP-Namen** , die **Anwendungsbeschreibung** und den URI der öffentlichen **Website** an (Dies kann temporär sein, bis Sie den Domänen Namen registrieren):</span><span class="sxs-lookup"><span data-stu-id="19017-111">Fill out the **App name** , **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="19017-112">Aktivieren Sie das Kontrollkästchen neben **Anmelden mit Twitter aktivieren** .</span><span class="sxs-lookup"><span data-stu-id="19017-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="19017-113">Microsoft. aspnetcore.Identity</span><span class="sxs-lookup"><span data-stu-id="19017-113">Microsoft.AspNetCore.Identity</span></span> <span data-ttu-id="19017-114">erfordert, dass Benutzer standardmäßig über eine e-Mail-Adresse verfügen.</span><span class="sxs-lookup"><span data-stu-id="19017-114">requires users to have an email address by default.</span></span> <span data-ttu-id="19017-115">Wechseln Sie zur Registerkarte **Berechtigungen** , klicken Sie auf die Schaltfläche **Bearbeiten** , und aktivieren Sie das Kontrollkästchen neben **e-Mail-Adresse anfordern von Benutzern**</span><span class="sxs-lookup"><span data-stu-id="19017-115">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users** .</span></span>

* <span data-ttu-id="19017-116">Geben Sie Ihren Entwicklungs-URI ein, und fügen Sie ihn an `/signin-twitter` das Feld **Rückruf-URLs** an (Beispiel: `https://webapp128.azurewebsites.net/signin-twitter` ).</span><span class="sxs-lookup"><span data-stu-id="19017-116">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="19017-117">Das Twitter-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert wird, verarbeitet Anforderungen bei der `/signin-twitter` Route automatisch, um den OAuth-Fluss zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="19017-117">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="19017-118">Das URI-Segment `/signin-twitter` wird als Standard Rückruf des Twitter-Authentifizierungs Anbieters festgelegt.</span><span class="sxs-lookup"><span data-stu-id="19017-118">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="19017-119">Sie können den Standard-Rückruf-URI ändern, während Sie die Twitter-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) -Klasse konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="19017-119">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="19017-120">Füllen Sie das restliche Formular aus, und klicken Sie auf **Erstellen** .</span><span class="sxs-lookup"><span data-stu-id="19017-120">Fill out the rest of the form and select **Create** .</span></span> <span data-ttu-id="19017-121">Es werden neue Anwendungsdetails angezeigt:</span><span class="sxs-lookup"><span data-stu-id="19017-121">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="19017-122">Speichern Sie den Schlüssel und das Geheimnis der Twitter-consumerapi</span><span class="sxs-lookup"><span data-stu-id="19017-122">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="19017-123">Speichern Sie sensible Einstellungen wie den Schlüssel der Twitter-Consumer-API und den geheimen Schlüssel mit [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="19017-123">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="19017-124">Führen Sie für dieses Beispiel die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="19017-124">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="19017-125">Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Aktivieren der geheimen Speicherung](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="19017-125">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="19017-126">Speichern Sie die sensiblen Einstellungen im lokalen geheimen Speicher mit den Schlüssel Schlüsseln `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` :</span><span class="sxs-lookup"><span data-stu-id="19017-126">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="19017-127">Diese Token finden Sie auf der Registerkarte **Schlüssel und Zugriffs Token** , nachdem Sie eine neue Twitter-Anwendung erstellt haben:</span><span class="sxs-lookup"><span data-stu-id="19017-127">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="19017-128">Konfigurieren der Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="19017-128">Configure Twitter Authentication</span></span>

<span data-ttu-id="19017-129">Fügen Sie den Twitter-Dienst in der- `ConfigureServices` Methode in der Datei *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="19017-129">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="19017-130">Weitere Informationen zu von der Twitter-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) -API-Referenz.</span><span class="sxs-lookup"><span data-stu-id="19017-130">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="19017-131">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="19017-131">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="19017-132">Mit Twitter anmelden</span><span class="sxs-lookup"><span data-stu-id="19017-132">Sign in with Twitter</span></span>

<span data-ttu-id="19017-133">Führen Sie die APP aus, und wählen Sie **Anmelden aus** .</span><span class="sxs-lookup"><span data-stu-id="19017-133">Run the app and select **Log in** .</span></span> <span data-ttu-id="19017-134">Eine Option zum Anmelden bei Twitter wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="19017-134">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="19017-135">Beim Klicken auf **Twitter** wird die Authentifizierung an Twitter umgeleitet:</span><span class="sxs-lookup"><span data-stu-id="19017-135">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="19017-136">Nachdem Sie Ihre Twitter-Anmelde Informationen eingegeben haben, werden Sie zurück an die Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.</span><span class="sxs-lookup"><span data-stu-id="19017-136">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="19017-137">Sie sind jetzt mit Ihren Twitter-Anmelde Informationen angemeldet:</span><span class="sxs-lookup"><span data-stu-id="19017-137">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="19017-138">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="19017-138">Troubleshooting</span></span>

* <span data-ttu-id="19017-139">**Nur ASP.net Core 2. x:** Wenn Identity nicht durch Aufrufen von `services.AddIdentity` konfiguriert `ConfigureServices` wird, führt der Versuch, sich zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden* .</span><span class="sxs-lookup"><span data-stu-id="19017-139">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="19017-140">Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="19017-140">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="19017-141">Wenn die Standortdatenbank nicht durch Anwenden der anfänglichen Migration erstellt wurde, wird *bei der Verarbeitung des Anforderungs Fehlers ein Daten Bank Vorgang* nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="19017-141">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="19017-142">Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="19017-142">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="19017-143">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="19017-143">Next steps</span></span>

* <span data-ttu-id="19017-144">In diesem Artikel wurde gezeigt, wie Sie sich mit Twitter authentifizieren können.</span><span class="sxs-lookup"><span data-stu-id="19017-144">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="19017-145">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="19017-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="19017-146">Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `ConsumerSecret` im Twitter-Entwickler Portal zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="19017-146">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="19017-147">Legen Sie die `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="19017-147">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="19017-148">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="19017-148">The configuration system is set up to read keys from environment variables.</span></span>
