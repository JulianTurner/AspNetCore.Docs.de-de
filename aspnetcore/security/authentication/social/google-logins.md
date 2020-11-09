---
title: Google externe Anmeldung Setup in ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht die Integration von Google-Konto der Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
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
uid: security/authentication/google-logins
ms.openlocfilehash: 111ea7c972778dfd5296d0401c16563aeaa36a63
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060312"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="59ab7-103">Google externe Anmeldung Setup in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="59ab7-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="59ab7-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="59ab7-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="59ab7-105">In diesem Tutorial wird gezeigt, wie Sie es Benutzern mithilfe des auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Projekts ASP.net Core 3,0 ermöglichen, sich mit Ihrem Google-Konto anzumelden.</span><span class="sxs-lookup"><span data-stu-id="59ab7-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="59ab7-106">Erstellen Sie ein Google API-Konsolen Projekt und eine Client-ID.</span><span class="sxs-lookup"><span data-stu-id="59ab7-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="59ab7-107">Installieren Sie [Microsoft. aspnetcore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span><span class="sxs-lookup"><span data-stu-id="59ab7-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="59ab7-108">Navigieren Sie zu [integrieren von Google Sign-In in Ihre Web-App](https://developers.google.com/identity/sign-in/web/sign-in) , und wählen Sie **Projekt konfigurieren** aus.</span><span class="sxs-lookup"><span data-stu-id="59ab7-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/sign-in) and select **Configure a project** .</span></span>
* <span data-ttu-id="59ab7-109">Wählen Sie im Dialogfeld **Konfigurieren des OAuth-Clients** den **Webserver** aus.</span><span class="sxs-lookup"><span data-stu-id="59ab7-109">In the **Configure your OAuth client** dialog, select **Web server** .</span></span>
* <span data-ttu-id="59ab7-110">Legen Sie im Textfeld **autorisierte Umleitungs-URIs** den Umleitungs-URI fest.</span><span class="sxs-lookup"><span data-stu-id="59ab7-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="59ab7-111">Zum Beispiel, `https://localhost:44312/signin-google`</span><span class="sxs-lookup"><span data-stu-id="59ab7-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="59ab7-112">Speichern Sie die **Client-ID** und den **geheimen Client** Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="59ab7-112">Save the **Client ID** and **Client Secret** .</span></span>
* <span data-ttu-id="59ab7-113">Wenn Sie den Standort bereitstellen, registrieren Sie die neue öffentliche URL in der **Google-Konsole** .</span><span class="sxs-lookup"><span data-stu-id="59ab7-113">When deploying the site, register the new public url from the **Google Console** .</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="59ab7-114">Speichern der Google-Client-ID und des geheimen Schlüssels</span><span class="sxs-lookup"><span data-stu-id="59ab7-114">Store the Google client ID and secret</span></span>

<span data-ttu-id="59ab7-115">Speichern Sie sensible Einstellungen wie die Google-Client-ID und die geheimen Werte mit [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="59ab7-115">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="59ab7-116">Führen Sie für dieses Beispiel die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="59ab7-116">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="59ab7-117">Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Aktivieren der geheimen Speicherung](xref:security/app-secrets#enable-secret-storage).</span><span class="sxs-lookup"><span data-stu-id="59ab7-117">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="59ab7-118">Speichern Sie die sensiblen Einstellungen im lokalen geheimen Speicher mit den geheimen Schlüsseln `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="59ab7-118">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="59ab7-119">Sie können Ihre API-Anmelde Informationen und die Verwendung in der [API-Konsole](https://console.developers.google.com/apis/dashboard)verwalten.</span><span class="sxs-lookup"><span data-stu-id="59ab7-119">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="59ab7-120">Konfigurieren von Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="59ab7-120">Configure Google authentication</span></span>

<span data-ttu-id="59ab7-121">Fügen Sie den Google-Dienst hinzu `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="59ab7-121">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="59ab7-122">Anmelden mit Google</span><span class="sxs-lookup"><span data-stu-id="59ab7-122">Sign in with Google</span></span>

* <span data-ttu-id="59ab7-123">Führen Sie die APP aus, und klicken Sie auf **Anmelden** .</span><span class="sxs-lookup"><span data-stu-id="59ab7-123">Run the app and click **Log in** .</span></span> <span data-ttu-id="59ab7-124">Eine Option zum Anmelden mit Google wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="59ab7-124">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="59ab7-125">Klicken Sie auf die **Google** -Schaltfläche, die zur Authentifizierung an Google umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="59ab7-125">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="59ab7-126">Nachdem Sie Ihre Google-Anmelde Informationen eingegeben haben, werden Sie an die Website zurückgeleitet.</span><span class="sxs-lookup"><span data-stu-id="59ab7-126">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="59ab7-127"><xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Weitere Informationen zu von der Google-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der API-Referenz.</span><span class="sxs-lookup"><span data-stu-id="59ab7-127">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="59ab7-128">Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.</span><span class="sxs-lookup"><span data-stu-id="59ab7-128">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="59ab7-129">Ändern des Standard-Rückruf-URI</span><span class="sxs-lookup"><span data-stu-id="59ab7-129">Change the default callback URI</span></span>

<span data-ttu-id="59ab7-130">Der URI-Segment `/signin-google` als den standardrückruf des Google-Authentifizierungsanbieter festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="59ab7-130">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="59ab7-131">Sie können den standardrückruf-URI ändern, während der Konfiguration die Middleware für die Google-Authentifizierung über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) Klasse.</span><span class="sxs-lookup"><span data-stu-id="59ab7-131">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="59ab7-132">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="59ab7-132">Troubleshooting</span></span>

* <span data-ttu-id="59ab7-133">Wenn die Anmeldung nicht funktioniert und Sie keine Fehler erhalten, wechseln Sie in den Entwicklungsmodus, um das Problem zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="59ab7-133">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="59ab7-134">Wenn Identity nicht durch Aufrufen von `services.AddIdentity` konfiguriert `ConfigureServices` wird, wird versucht, die Ergebnisse in *argumumtexception zu authentifizieren: die signinscheme-Option muss angegeben werden* .</span><span class="sxs-lookup"><span data-stu-id="59ab7-134">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="59ab7-135">Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.</span><span class="sxs-lookup"><span data-stu-id="59ab7-135">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="59ab7-136">Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler.</span><span class="sxs-lookup"><span data-stu-id="59ab7-136">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="59ab7-137">Wählen Sie **Migrationen anwenden** aus, um die Datenbank zu erstellen, und aktualisieren Sie die Seite, um den Fehler fortzusetzen.</span><span class="sxs-lookup"><span data-stu-id="59ab7-137">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="59ab7-138">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="59ab7-138">Next steps</span></span>

* <span data-ttu-id="59ab7-139">In diesem Artikel wurde gezeigt, wie Sie mit Google authentifiziert werden können.</span><span class="sxs-lookup"><span data-stu-id="59ab7-139">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="59ab7-140">Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="59ab7-140">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="59ab7-141">Nachdem Sie die app in Azure veröffentlicht haben, setzen Sie den `ClientSecret` in der Google-API-Konsole zurück.</span><span class="sxs-lookup"><span data-stu-id="59ab7-141">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="59ab7-142">Legen Sie die `Authentication:Google:ClientId` und `Authentication:Google:ClientSecret` Anwendungseinstellungen im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="59ab7-142">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="59ab7-143">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="59ab7-143">The configuration system is set up to read keys from environment variables.</span></span>
