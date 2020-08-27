---
title: Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.net Core-App mit e-Mail-Bestätigung und Kenn Wort Zurücksetzung
ms.author: riande
ms.date: 03/11/2019
no-loc:
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
uid: security/authentication/accconfirm
ms.openlocfilehash: d6ea37ceb83ffbaa94187e0c541c79428594e4b4
ms.sourcegitcommit: 2039e60eb7b482da8298f82dcd5eda27cf747f32
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88906448"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="e066c-103">Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="e066c-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="e066c-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="e066c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="e066c-105">Dieses Tutorial zeigt, wie Sie eine ASP.net Core-App mit e-Mail-Bestätigung und Kenn Wort Zurücksetzung erstellen.</span><span class="sxs-lookup"><span data-stu-id="e066c-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="e066c-106">Dieses Tutorial ist **kein** Thema.</span><span class="sxs-lookup"><span data-stu-id="e066c-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="e066c-107">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="e066c-107">You should be familiar with:</span></span>

* [<span data-ttu-id="e066c-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e066c-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="e066c-109">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="e066c-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="e066c-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="e066c-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="e066c-111">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="e066c-111">Prerequisites</span></span>

[<span data-ttu-id="e066c-112">.NET Core 3.0 SDK oder höher</span><span class="sxs-lookup"><span data-stu-id="e066c-112">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="e066c-113">Erstellen und Testen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="e066c-113">Create and test a web app with authentication</span></span>

<span data-ttu-id="e066c-114">Führen Sie die folgenden Befehle aus, um eine Web-App mit Authentifizierung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e066c-114">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="e066c-115">Führen Sie die APP aus, wählen Sie den Link **registrieren** aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="e066c-115">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="e066c-116">Nach der Registrierung werden Sie zur Seite an weitergeleitet, `/Identity/Account/RegisterConfirmation` die einen Link zum Simulieren einer e-Mail-Bestätigung enthält:</span><span class="sxs-lookup"><span data-stu-id="e066c-116">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="e066c-117">Klicken Sie auf den Link `Click here to confirm your account` (Zentralisierte Protokollierung).</span><span class="sxs-lookup"><span data-stu-id="e066c-117">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="e066c-118">Wählen Sie den **Anmelde** Link aus, und melden Sie sich mit denselben Anmelde Informationen an.</span><span class="sxs-lookup"><span data-stu-id="e066c-118">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="e066c-119">Wählen Sie den `Hello YourEmail@provider.com!` Link aus, der Sie zur Seite umleitet `/Identity/Account/Manage/PersonalData` .</span><span class="sxs-lookup"><span data-stu-id="e066c-119">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="e066c-120">Wählen Sie auf der linken Seite die Registerkarte **persönliche Daten** aus, und klicken Sie dann auf **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="e066c-120">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="e066c-121">Konfigurieren eines e-Mail-Anbieters</span><span class="sxs-lookup"><span data-stu-id="e066c-121">Configure an email provider</span></span>

<span data-ttu-id="e066c-122">In diesem Tutorial wird [sendgrid](https://sendgrid.com) zum Senden von e-Mails verwendet.</span><span class="sxs-lookup"><span data-stu-id="e066c-122">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="e066c-123">Sie benötigen ein sendgrid-Konto und einen Schlüssel zum Senden von e-Mails.</span><span class="sxs-lookup"><span data-stu-id="e066c-123">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="e066c-124">Sie können andere e-Mail-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="e066c-124">You can use other email providers.</span></span> <span data-ttu-id="e066c-125">Es wird empfohlen, sendgrid oder einen anderen e-Mail-Dienst zum Senden von e-Mails zu verwenden</span><span class="sxs-lookup"><span data-stu-id="e066c-125">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="e066c-126">SMTP ist schwierig zu sichern und ordnungsgemäß eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="e066c-126">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="e066c-127">Das sendgrid-Konto erfordert möglicherweise das [Hinzufügen eines Absenders](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="e066c-127">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="e066c-128">Erstellen Sie eine Klasse zum Abrufen des Schlüssels für sichere e-Mails.</span><span class="sxs-lookup"><span data-stu-id="e066c-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="e066c-129">Erstellen Sie für dieses Beispiel *Dienste/authmessagesenderoptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="e066c-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="e066c-130">Konfigurieren von sendgrid-Benutzer Geheimnissen</span><span class="sxs-lookup"><span data-stu-id="e066c-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="e066c-131">Legen Sie `SendGridUser` und `SendGridKey` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="e066c-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="e066c-132">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e066c-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="e066c-133">Unter Windows speichert Secret Manager Schlüssel-Wert-Paare in einer *secrets.js* in der Datei im `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="e066c-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="e066c-134">Der Inhalt des *secrets.jsin* der Datei ist nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="e066c-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="e066c-135">Das folgende Markup zeigt die *secrets.jsfür* die Datei.</span><span class="sxs-lookup"><span data-stu-id="e066c-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="e066c-136">Der `SendGridKey` Wert wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="e066c-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="e066c-137">Weitere Informationen finden Sie im [options Muster](xref:fundamentals/configuration/options) und in der [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="e066c-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="e066c-138">Installieren von sendgrid</span><span class="sxs-lookup"><span data-stu-id="e066c-138">Install SendGrid</span></span>

<span data-ttu-id="e066c-139">In diesem Tutorial wird gezeigt, wie Sie e-Mail-Benachrichtigungen über [sendgrid](https://sendgrid.com/)hinzufügen, aber Sie können e-Mails mithilfe von SMTP und anderen Mechanismen senden.</span><span class="sxs-lookup"><span data-stu-id="e066c-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="e066c-140">Installieren Sie das `SendGrid` nuget-Paket:</span><span class="sxs-lookup"><span data-stu-id="e066c-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e066c-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e066c-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e066c-142">Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="e066c-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="e066c-143">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e066c-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e066c-144">Geben Sie in der Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="e066c-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="e066c-145">Weitere Informationen zur Registrierung für ein kostenloses sendgrid-Konto finden [Sie unter Get Started with sendgrid](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="e066c-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="e066c-146">Implementieren von iemailsender</span><span class="sxs-lookup"><span data-stu-id="e066c-146">Implement IEmailSender</span></span>

<span data-ttu-id="e066c-147">Erstellen Sie zum Implementieren von `IEmailSender` *Services/emailsender. cs* mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="e066c-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="e066c-148">Konfigurieren des Starts zur Unterstützung von e-Mails</span><span class="sxs-lookup"><span data-stu-id="e066c-148">Configure startup to support email</span></span>

<span data-ttu-id="e066c-149">Fügen Sie der- `ConfigureServices` Methode in der Datei *Startup.cs* den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="e066c-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="e066c-150">Fügen Sie `EmailSender` als vorübergehenden Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="e066c-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="e066c-151">Registrieren Sie die `AuthMessageSenderOptions` Konfigurations Instanz.</span><span class="sxs-lookup"><span data-stu-id="e066c-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a><span data-ttu-id="e066c-152">Gerüst Register Bestätigung</span><span class="sxs-lookup"><span data-stu-id="e066c-152">Scaffold RegisterConfirmation</span></span>

<span data-ttu-id="e066c-153">Befolgen Sie die Anweisungen für [ Identity Gerüstbau](xref:security/authentication/scaffold-identity) und Gerüstbau `RegisterConfirmation` .</span><span class="sxs-lookup"><span data-stu-id="e066c-153">Follow the instructions for [Scaffold Identity](xref:security/authentication/scaffold-identity) and scaffold `RegisterConfirmation`.</span></span>

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="e066c-154">Registrieren, bestätigen der e-Mail und Zurücksetzen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="e066c-154">Register, confirm email, and reset password</span></span>

<span data-ttu-id="e066c-155">Führen Sie die Web-App aus, und testen Sie den Ablauf der Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="e066c-155">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="e066c-156">Ausführen der APP und Registrieren eines neuen Benutzers</span><span class="sxs-lookup"><span data-stu-id="e066c-156">Run the app and register a new user</span></span>
* <span data-ttu-id="e066c-157">Überprüfen Sie Ihre e-Mail auf den Link zur Konto Bestätigung.</span><span class="sxs-lookup"><span data-stu-id="e066c-157">Check your email for the account confirmation link.</span></span> <span data-ttu-id="e066c-158">Siehe [Debuggen von e-Mails](#debug) , wenn Sie die e-Mail nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="e066c-158">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="e066c-159">Klicken Sie auf den Link, um Ihre e-Mail zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="e066c-159">Click the link to confirm your email.</span></span>
* <span data-ttu-id="e066c-160">Melden Sie sich mit Ihrer e-Mail und dem Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="e066c-160">Sign in with your email and password.</span></span>
* <span data-ttu-id="e066c-161">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="e066c-161">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="e066c-162">Kenn Wort Zurücksetzung testen</span><span class="sxs-lookup"><span data-stu-id="e066c-162">Test password reset</span></span>

* <span data-ttu-id="e066c-163">Wenn Sie angemeldet sind, wählen Sie **Abmelden aus**.</span><span class="sxs-lookup"><span data-stu-id="e066c-163">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="e066c-164">Wählen Sie den Link **Anmelden aus** , und wählen Sie den Link **Kennwort vergessen?** aus.</span><span class="sxs-lookup"><span data-stu-id="e066c-164">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="e066c-165">Geben Sie die e-Mail ein, die Sie zum Registrieren des Kontos verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="e066c-165">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="e066c-166">Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts wird gesendet.</span><span class="sxs-lookup"><span data-stu-id="e066c-166">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="e066c-167">Überprüfen Sie Ihre e-Mail, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts</span><span class="sxs-lookup"><span data-stu-id="e066c-167">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="e066c-168">Nachdem das Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrer e-Mail-Adresse und dem neuen Kennwort anmelden.</span><span class="sxs-lookup"><span data-stu-id="e066c-168">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

<a name="resend"></a>

## <a name="resend-email-confirmation"></a><span data-ttu-id="e066c-169">E-Mail-Bestätigung erneut senden</span><span class="sxs-lookup"><span data-stu-id="e066c-169">Resend email confirmation</span></span>

<span data-ttu-id="e066c-170">Wählen Sie in ASP.net Core 5,0 und höher den Link **e-Mail-Bestätigung erneut senden** auf der **Anmelde** Seite aus.</span><span class="sxs-lookup"><span data-stu-id="e066c-170">In ASP.NET Core 5.0 and later, select the **Resend email confirmation** link on the **Login** page.</span></span>

### <a name="change-email-and-activity-timeout"></a><span data-ttu-id="e066c-171">E-Mail-und Aktivitäts Timeout ändern</span><span class="sxs-lookup"><span data-stu-id="e066c-171">Change email and activity timeout</span></span>

<span data-ttu-id="e066c-172">Das standardmäßige Inaktivitäts Timeout beträgt 14 Tage.</span><span class="sxs-lookup"><span data-stu-id="e066c-172">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="e066c-173">Mit dem folgenden Code wird das Inaktivitäts Timeout auf 5 Tage festgelegt:</span><span class="sxs-lookup"><span data-stu-id="e066c-173">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="e066c-174">Alle Datenschutz Token-Lebensdauer ändern</span><span class="sxs-lookup"><span data-stu-id="e066c-174">Change all data protection token lifespans</span></span>

<span data-ttu-id="e066c-175">Der folgende Code ändert den Timeout Zeitraum für alle Token für die Datenschutz Token in 3 Stunden:</span><span class="sxs-lookup"><span data-stu-id="e066c-175">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="e066c-176">Die integrierten Identity Benutzer Token (siehe [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben ein [Timeout von einem Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="e066c-176">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="e066c-177">Lebensdauer von e-Mail-Token</span><span class="sxs-lookup"><span data-stu-id="e066c-177">Change the email token lifespan</span></span>

<span data-ttu-id="e066c-178">Die standardmäßige tokengültigkeits Dauer der [ Identity Benutzer Token](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [ein Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="e066c-178">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="e066c-179">In diesem Abschnitt wird gezeigt, wie Sie die Lebensdauer von e-Mail-</span><span class="sxs-lookup"><span data-stu-id="e066c-179">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="e066c-180">Fügen Sie einen benutzerdefinierten [dataprotectortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und Folgendes hinzu <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="e066c-180">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="e066c-181">Fügen Sie dem Dienst Container den benutzerdefinierten Anbieter hinzu:</span><span class="sxs-lookup"><span data-stu-id="e066c-181">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="e066c-182">E-Mail Debuggen</span><span class="sxs-lookup"><span data-stu-id="e066c-182">Debug email</span></span>

<span data-ttu-id="e066c-183">Wenn Sie keine e-Mail mehr erhalten können:</span><span class="sxs-lookup"><span data-stu-id="e066c-183">If you can't get email working:</span></span>

* <span data-ttu-id="e066c-184">Legen Sie einen Haltepunkt fest `EmailSender.Execute` , um zu überprüfen, ob `SendGridClient.SendEmailAsync` aufgerufen wird</span><span class="sxs-lookup"><span data-stu-id="e066c-184">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="e066c-185">Erstellen [Sie eine Konsolen-App zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mithilfe eines ähnlichen Codes an `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="e066c-185">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="e066c-186">Lesen Sie die Seite [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="e066c-186">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="e066c-187">Überprüfen Sie Ihren Spam-Ordner.</span><span class="sxs-lookup"><span data-stu-id="e066c-187">Check your spam folder.</span></span>
* <span data-ttu-id="e066c-188">Probieren Sie einen anderen e-Mail-Alias für einen anderen e-Mail-Anbieter aus (Microsoft, Yahoo, Gmail usw.)</span><span class="sxs-lookup"><span data-stu-id="e066c-188">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="e066c-189">Senden Sie an verschiedene e-Mail-Konten.</span><span class="sxs-lookup"><span data-stu-id="e066c-189">Try sending to different email accounts.</span></span>

<span data-ttu-id="e066c-190">**Eine bewährte Sicherheits** Maßnahme besteht darin, **keine** Produktionsgeheimnisse in Test-und Entwicklungsumgebungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="e066c-190">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="e066c-191">Wenn Sie die app in Azure veröffentlichen, legen Sie die sendgrid-Geheimnisse als Anwendungseinstellungen im Azure-Web-App-Portal fest.</span><span class="sxs-lookup"><span data-stu-id="e066c-191">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="e066c-192">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="e066c-192">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="e066c-193">Kombinieren von sozialen und lokalen Anmeldekonten</span><span class="sxs-lookup"><span data-stu-id="e066c-193">Combine social and local login accounts</span></span>

<span data-ttu-id="e066c-194">Zum Durchführen dieses Abschnitts müssen Sie zuerst einen externen Authentifizierungs Anbieter aktivieren.</span><span class="sxs-lookup"><span data-stu-id="e066c-194">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="e066c-195">Weitere Informationen finden Sie unter [Authentifizierung für Facebook, Google und externe Anbieter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e066c-195">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="e066c-196">Sie können lokale und soziale Konten kombinieren, indem Sie auf Ihren e-Mail-Link klicken.</span><span class="sxs-lookup"><span data-stu-id="e066c-196">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="e066c-197">In der folgenden Sequenz wird " RickAndMSFT@gmail.com " zuerst als lokale Anmeldung erstellt. Sie können das Konto jedoch zuerst als Social Login (Social Login) erstellen und dann einen lokalen Anmelde Namen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="e066c-197">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webanwendung: RickAndMSFT@gmail.com Benutzer authentifiziert](accconfirm/_static/rick.png)

<span data-ttu-id="e066c-199">Klicken Sie auf den Link **Verwalten** .</span><span class="sxs-lookup"><span data-stu-id="e066c-199">Click on the **Manage** link.</span></span> <span data-ttu-id="e066c-200">Beachten Sie die 0 (null) externen (Anmeldungen), die diesem Konto zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="e066c-200">Note the 0 external (social logins) associated with this account.</span></span>

![Ansicht verwalten](accconfirm/_static/manage.png)

<span data-ttu-id="e066c-202">Klicken Sie auf den Link zu einem anderen Anmeldedienst, und akzeptieren Sie die APP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="e066c-202">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="e066c-203">In der folgenden Abbildung ist Facebook der externe Authentifizierungs Anbieter:</span><span class="sxs-lookup"><span data-stu-id="e066c-203">In the following image, Facebook is the external authentication provider:</span></span>

![Verwalten der Ansicht externer Anmeldungen mit Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="e066c-205">Die beiden Konten wurden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="e066c-205">The two accounts have been combined.</span></span> <span data-ttu-id="e066c-206">Sie können sich mit einem der beiden Konten anmelden.</span><span class="sxs-lookup"><span data-stu-id="e066c-206">You are able to sign in with either account.</span></span> <span data-ttu-id="e066c-207">Möglicherweise möchten Sie, dass Ihre Benutzer lokale Konten hinzufügen, falls der Authentifizierungsdienst für soziale Anmelde Informationen ausgefallen ist oder Sie den Zugriff auf Ihr Social Media-Konto verlieren.</span><span class="sxs-lookup"><span data-stu-id="e066c-207">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="e066c-208">Aktivieren der Konto Bestätigung nach einem Standort Benutzer</span><span class="sxs-lookup"><span data-stu-id="e066c-208">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="e066c-209">Das Aktivieren der Konto Bestätigung auf einem Standort mit Benutzern sperrt alle vorhandenen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="e066c-209">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="e066c-210">Vorhandene Benutzer werden gesperrt, da ihre Konten nicht bestätigt werden.</span><span class="sxs-lookup"><span data-stu-id="e066c-210">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="e066c-211">Verwenden Sie einen der folgenden Ansätze, um die vorhandene Benutzer Sperre zu umgehen:</span><span class="sxs-lookup"><span data-stu-id="e066c-211">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="e066c-212">Aktualisieren Sie die Datenbank, um alle vorhandenen Benutzer als bestätigt zu markieren.</span><span class="sxs-lookup"><span data-stu-id="e066c-212">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="e066c-213">Vorhandene Benutzer bestätigen.</span><span class="sxs-lookup"><span data-stu-id="e066c-213">Confirm existing users.</span></span> <span data-ttu-id="e066c-214">Beispiel: Senden von e-Mails mit Bestätigungs Links per Batch.</span><span class="sxs-lookup"><span data-stu-id="e066c-214">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="e066c-215">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="e066c-215">Prerequisites</span></span>

[<span data-ttu-id="e066c-216">.Net Core 2,2 SDK oder höher</span><span class="sxs-lookup"><span data-stu-id="e066c-216">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a><span data-ttu-id="e066c-217">Erstellen einer Web-App und eines Gerüsts Identity</span><span class="sxs-lookup"><span data-stu-id="e066c-217">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="e066c-218">Führen Sie die folgenden Befehle aus, um eine Web-App mit Authentifizierung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e066c-218">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run
```

> [!NOTE]
> <span data-ttu-id="e066c-219">Wenn <xref:Microsoft.AspNetCore.Identity.PasswordOptions> in konfiguriert `Startup.ConfigureServices` ist, ist möglicherweise die- [ `[StringLength]` Attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) Konfiguration für die- `Password` Eigenschaft auf den Seiten mit einem Gerüst erforderlich Identity .</span><span class="sxs-lookup"><span data-stu-id="e066c-219">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="e066c-220">Eine- `InputModel` `Password` Eigenschaft wurde nach dem `Areas/Identity/Pages/Account/Register.cshtml.cs` Gerüstbau in der Datei gefunden Identity .</span><span class="sxs-lookup"><span data-stu-id="e066c-220">An `InputModel` `Password` property is found in the `Areas/Identity/Pages/Account/Register.cshtml.cs` file after scaffolding Identity.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="e066c-221">Neue Benutzerregistrierung testen</span><span class="sxs-lookup"><span data-stu-id="e066c-221">Test new user registration</span></span>

<span data-ttu-id="e066c-222">Führen Sie die APP aus, wählen Sie den Link **registrieren** aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="e066c-222">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="e066c-223">An diesem Punkt wird nur die e-Mail-Überprüfung mit dem- [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) Attribut durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="e066c-223">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="e066c-224">Nachdem Sie die Registrierung übermittelt haben, werden Sie bei der App angemeldet.</span><span class="sxs-lookup"><span data-stu-id="e066c-224">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="e066c-225">Später in diesem Tutorial wird der Code aktualisiert, sodass neue Benutzer sich erst anmelden können, wenn Ihre e-Mail-Nachricht überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="e066c-225">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="e066c-226">Beachten Sie, dass das Feld der Tabelle `EmailConfirmed` ist `False` .</span><span class="sxs-lookup"><span data-stu-id="e066c-226">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="e066c-227">Sie sollten diese e-Mail im nächsten Schritt erneut verwenden, wenn die APP eine Bestätigungs-e-Mail sendet.</span><span class="sxs-lookup"><span data-stu-id="e066c-227">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="e066c-228">Klicken Sie mit der rechten Maustaste auf die Zeile, und wählen Sie **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="e066c-228">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="e066c-229">Wenn Sie den e-Mail-Alias löschen, wird dies in den folgenden Schritten vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="e066c-229">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="e066c-230">Bestätigung per e-Mail erforderlich</span><span class="sxs-lookup"><span data-stu-id="e066c-230">Require email confirmation</span></span>

<span data-ttu-id="e066c-231">Es wird empfohlen, die e-Mail-Adresse einer neuen Benutzerregistrierung zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="e066c-231">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="e066c-232">Mithilfe der e-Mail-Bestätigung können Sie sicherstellen, dass Sie keine andere Identität annehmen (d. h., Sie haben sich nicht bei einer anderen e-Mail registriert).</span><span class="sxs-lookup"><span data-stu-id="e066c-232">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="e066c-233">Angenommen, Sie hatten ein Diskussionsforum, und Sie wollten verhindern, dass sich " yli@example.com " als "" registriert nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="e066c-233">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="e066c-234">Ohne e-Mail-Bestätigung kann " nolivetto@contoso.com " unerwünschte e-Mails von Ihrer APP erhalten.</span><span class="sxs-lookup"><span data-stu-id="e066c-234">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="e066c-235">Angenommen, der Benutzer hat sich versehentlich als " ylo@example.com " registriert und die falsche Schreibweise von "Yli" nicht bemerkt.</span><span class="sxs-lookup"><span data-stu-id="e066c-235">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="e066c-236">Sie sind nicht in der Lage, die Kenn Wort Wiederherstellung zu verwenden, da die APP nicht über die richtige e-Mail verfügt</span><span class="sxs-lookup"><span data-stu-id="e066c-236">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="e066c-237">E-Mail-Bestätigung bietet eingeschränkten Schutz vor Bots.</span><span class="sxs-lookup"><span data-stu-id="e066c-237">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="e066c-238">E-Mail-Bestätigung bietet keinen Schutz vor böswilligen Benutzern mit vielen e-Mail-Konten</span><span class="sxs-lookup"><span data-stu-id="e066c-238">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="e066c-239">In der Regel möchten Sie, dass neue Benutzerdaten an Ihrer Website veröffentlichen, bevor Sie eine bestätigte e-Mail-Adresse haben.</span><span class="sxs-lookup"><span data-stu-id="e066c-239">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="e066c-240">Aktualisieren `Startup.ConfigureServices`  Sie, um eine bestätigte e-Mail anzufordern:</span><span class="sxs-lookup"><span data-stu-id="e066c-240">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="e066c-241">`config.SignIn.RequireConfirmedEmail = true;` verhindert, dass registrierte Benutzer sich anmelden, bis Ihre e-Mail-Nachricht bestätigt wird.</span><span class="sxs-lookup"><span data-stu-id="e066c-241">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="e066c-242">E-Mail-Anbieter</span><span class="sxs-lookup"><span data-stu-id="e066c-242">Configure email provider</span></span>

<span data-ttu-id="e066c-243">In diesem Tutorial wird [sendgrid](https://sendgrid.com) zum Senden von e-Mails verwendet.</span><span class="sxs-lookup"><span data-stu-id="e066c-243">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="e066c-244">Sie benötigen ein sendgrid-Konto und einen Schlüssel zum Senden von e-Mails.</span><span class="sxs-lookup"><span data-stu-id="e066c-244">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="e066c-245">Sie können andere e-Mail-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="e066c-245">You can use other email providers.</span></span> <span data-ttu-id="e066c-246">ASP.net Core 2. x umfasst `System.Net.Mail` , das Ihnen ermöglicht, e-Mails von Ihrer APP zu senden.</span><span class="sxs-lookup"><span data-stu-id="e066c-246">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="e066c-247">Es wird empfohlen, sendgrid oder einen anderen e-Mail-Dienst zum Senden von e-Mails zu verwenden</span><span class="sxs-lookup"><span data-stu-id="e066c-247">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="e066c-248">SMTP ist schwierig zu sichern und ordnungsgemäß eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="e066c-248">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="e066c-249">Erstellen Sie eine Klasse zum Abrufen des Schlüssels für sichere e-Mails.</span><span class="sxs-lookup"><span data-stu-id="e066c-249">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="e066c-250">Erstellen Sie für dieses Beispiel *Dienste/authmessagesenderoptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="e066c-250">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="e066c-251">Konfigurieren von sendgrid-Benutzer Geheimnissen</span><span class="sxs-lookup"><span data-stu-id="e066c-251">Configure SendGrid user secrets</span></span>

<span data-ttu-id="e066c-252">Legen Sie `SendGridUser` und `SendGridKey` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="e066c-252">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="e066c-253">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e066c-253">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="e066c-254">Unter Windows speichert Secret Manager Schlüssel-Wert-Paare in einer *secrets.js* in der Datei im `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="e066c-254">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="e066c-255">Der Inhalt des *secrets.jsin* der Datei ist nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="e066c-255">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="e066c-256">Das folgende Markup zeigt die *secrets.jsfür* die Datei.</span><span class="sxs-lookup"><span data-stu-id="e066c-256">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="e066c-257">Der `SendGridKey` Wert wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="e066c-257">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="e066c-258">Weitere Informationen finden Sie im [options Muster](xref:fundamentals/configuration/options) und in der [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="e066c-258">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="e066c-259">Installieren von sendgrid</span><span class="sxs-lookup"><span data-stu-id="e066c-259">Install SendGrid</span></span>

<span data-ttu-id="e066c-260">In diesem Tutorial wird gezeigt, wie Sie e-Mail-Benachrichtigungen über [sendgrid](https://sendgrid.com/)hinzufügen, aber Sie können e-Mails mithilfe von SMTP und anderen Mechanismen senden.</span><span class="sxs-lookup"><span data-stu-id="e066c-260">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="e066c-261">Installieren Sie das `SendGrid` nuget-Paket:</span><span class="sxs-lookup"><span data-stu-id="e066c-261">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e066c-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e066c-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e066c-263">Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="e066c-263">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="e066c-264">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e066c-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e066c-265">Geben Sie in der Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="e066c-265">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="e066c-266">Weitere Informationen zur Registrierung für ein kostenloses sendgrid-Konto finden [Sie unter Get Started with sendgrid](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="e066c-266">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="e066c-267">Implementieren von iemailsender</span><span class="sxs-lookup"><span data-stu-id="e066c-267">Implement IEmailSender</span></span>

<span data-ttu-id="e066c-268">Erstellen Sie zum Implementieren von `IEmailSender` *Services/emailsender. cs* mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="e066c-268">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="e066c-269">Konfigurieren des Starts zur Unterstützung von e-Mails</span><span class="sxs-lookup"><span data-stu-id="e066c-269">Configure startup to support email</span></span>

<span data-ttu-id="e066c-270">Fügen Sie der- `ConfigureServices` Methode in der Datei *Startup.cs* den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="e066c-270">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="e066c-271">Fügen Sie `EmailSender` als vorübergehenden Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="e066c-271">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="e066c-272">Registrieren Sie die `AuthMessageSenderOptions` Konfigurations Instanz.</span><span class="sxs-lookup"><span data-stu-id="e066c-272">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="e066c-273">Aktivieren der Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="e066c-273">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="e066c-274">Die Vorlage enthält den Code für die Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="e066c-274">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="e066c-275">Suchen Sie die `OnPostAsync` -Methode in " *Areas/ Identity /pages/Account/Register.cshtml.cs*".</span><span class="sxs-lookup"><span data-stu-id="e066c-275">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="e066c-276">Verhindern Sie, dass neu registrierte Benutzer automatisch angemeldet werden, indem Sie die folgende Zeile auskommentieren:</span><span class="sxs-lookup"><span data-stu-id="e066c-276">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="e066c-277">Die gesamte-Methode wird angezeigt, wenn die geänderte Zeile hervorgehoben ist:</span><span class="sxs-lookup"><span data-stu-id="e066c-277">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="e066c-278">Registrieren, bestätigen der e-Mail und Zurücksetzen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="e066c-278">Register, confirm email, and reset password</span></span>

<span data-ttu-id="e066c-279">Führen Sie die Web-App aus, und testen Sie den Ablauf der Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="e066c-279">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="e066c-280">Ausführen der APP und Registrieren eines neuen Benutzers</span><span class="sxs-lookup"><span data-stu-id="e066c-280">Run the app and register a new user</span></span>
* <span data-ttu-id="e066c-281">Überprüfen Sie Ihre e-Mail auf den Link zur Konto Bestätigung.</span><span class="sxs-lookup"><span data-stu-id="e066c-281">Check your email for the account confirmation link.</span></span> <span data-ttu-id="e066c-282">Siehe [Debuggen von e-Mails](#debug) , wenn Sie die e-Mail nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="e066c-282">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="e066c-283">Klicken Sie auf den Link, um Ihre e-Mail zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="e066c-283">Click the link to confirm your email.</span></span>
* <span data-ttu-id="e066c-284">Melden Sie sich mit Ihrer e-Mail und dem Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="e066c-284">Sign in with your email and password.</span></span>
* <span data-ttu-id="e066c-285">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="e066c-285">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="e066c-286">Anzeigen der Seite "verwalten"</span><span class="sxs-lookup"><span data-stu-id="e066c-286">View the manage page</span></span>

<span data-ttu-id="e066c-287">Wählen Sie Ihren Benutzernamen im Browser aus: ![ Browserfenster mit Benutzername](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="e066c-287">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="e066c-288">Die Seite verwalten wird angezeigt, und die Registerkarte **Profil** ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="e066c-288">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="e066c-289">Die **e-Mail** zeigt ein Kontrollkästchen an, das anzeigt, dass die e-Mail bestätigt wurde.</span><span class="sxs-lookup"><span data-stu-id="e066c-289">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="e066c-290">Kenn Wort Zurücksetzung testen</span><span class="sxs-lookup"><span data-stu-id="e066c-290">Test password reset</span></span>

* <span data-ttu-id="e066c-291">Wenn Sie angemeldet sind, wählen Sie **Abmelden aus**.</span><span class="sxs-lookup"><span data-stu-id="e066c-291">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="e066c-292">Wählen Sie den Link **Anmelden aus** , und wählen Sie den Link **Kennwort vergessen?** aus.</span><span class="sxs-lookup"><span data-stu-id="e066c-292">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="e066c-293">Geben Sie die e-Mail ein, die Sie zum Registrieren des Kontos verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="e066c-293">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="e066c-294">Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts wird gesendet.</span><span class="sxs-lookup"><span data-stu-id="e066c-294">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="e066c-295">Überprüfen Sie Ihre e-Mail, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts</span><span class="sxs-lookup"><span data-stu-id="e066c-295">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="e066c-296">Nachdem das Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrer e-Mail-Adresse und dem neuen Kennwort anmelden.</span><span class="sxs-lookup"><span data-stu-id="e066c-296">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="e066c-297">E-Mail-und Aktivitäts Timeout ändern</span><span class="sxs-lookup"><span data-stu-id="e066c-297">Change email and activity timeout</span></span>

<span data-ttu-id="e066c-298">Das standardmäßige Inaktivitäts Timeout beträgt 14 Tage.</span><span class="sxs-lookup"><span data-stu-id="e066c-298">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="e066c-299">Mit dem folgenden Code wird das Inaktivitäts Timeout auf 5 Tage festgelegt:</span><span class="sxs-lookup"><span data-stu-id="e066c-299">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="e066c-300">Alle Datenschutz Token-Lebensdauer ändern</span><span class="sxs-lookup"><span data-stu-id="e066c-300">Change all data protection token lifespans</span></span>

<span data-ttu-id="e066c-301">Der folgende Code ändert den Timeout Zeitraum für alle Token für die Datenschutz Token in 3 Stunden:</span><span class="sxs-lookup"><span data-stu-id="e066c-301">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="e066c-302">Die integrierten Identity Benutzer Token (siehe [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben ein [Timeout von einem Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="e066c-302">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="e066c-303">Lebensdauer von e-Mail-Token</span><span class="sxs-lookup"><span data-stu-id="e066c-303">Change the email token lifespan</span></span>

<span data-ttu-id="e066c-304">Die standardmäßige tokengültigkeits Dauer der [ Identity Benutzer Token](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [ein Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="e066c-304">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="e066c-305">In diesem Abschnitt wird gezeigt, wie Sie die Lebensdauer von e-Mail-</span><span class="sxs-lookup"><span data-stu-id="e066c-305">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="e066c-306">Fügen Sie einen benutzerdefinierten [dataprotectortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und Folgendes hinzu <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="e066c-306">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="e066c-307">Fügen Sie dem Dienst Container den benutzerdefinierten Anbieter hinzu:</span><span class="sxs-lookup"><span data-stu-id="e066c-307">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="e066c-308">E-Mail-Bestätigung erneut senden</span><span class="sxs-lookup"><span data-stu-id="e066c-308">Resend email confirmation</span></span>

<span data-ttu-id="e066c-309">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="e066c-309">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="e066c-310">E-Mail Debuggen</span><span class="sxs-lookup"><span data-stu-id="e066c-310">Debug email</span></span>

<span data-ttu-id="e066c-311">Wenn Sie keine e-Mail mehr erhalten können:</span><span class="sxs-lookup"><span data-stu-id="e066c-311">If you can't get email working:</span></span>

* <span data-ttu-id="e066c-312">Legen Sie einen Haltepunkt fest `EmailSender.Execute` , um zu überprüfen, ob `SendGridClient.SendEmailAsync` aufgerufen wird</span><span class="sxs-lookup"><span data-stu-id="e066c-312">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="e066c-313">Erstellen [Sie eine Konsolen-App zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mithilfe eines ähnlichen Codes an `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="e066c-313">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="e066c-314">Lesen Sie die Seite [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="e066c-314">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="e066c-315">Überprüfen Sie Ihren Spam-Ordner.</span><span class="sxs-lookup"><span data-stu-id="e066c-315">Check your spam folder.</span></span>
* <span data-ttu-id="e066c-316">Probieren Sie einen anderen e-Mail-Alias für einen anderen e-Mail-Anbieter aus (Microsoft, Yahoo, Gmail usw.)</span><span class="sxs-lookup"><span data-stu-id="e066c-316">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="e066c-317">Senden Sie an verschiedene e-Mail-Konten.</span><span class="sxs-lookup"><span data-stu-id="e066c-317">Try sending to different email accounts.</span></span>

<span data-ttu-id="e066c-318">**Eine bewährte Sicherheits** Maßnahme besteht darin, **keine** Produktionsgeheimnisse in Test-und Entwicklungsumgebungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="e066c-318">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="e066c-319">Wenn Sie die app in Azure veröffentlichen, können Sie die sendgrid-Geheimnisse als Anwendungseinstellungen im Azure-Web-App-Portal festlegen.</span><span class="sxs-lookup"><span data-stu-id="e066c-319">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="e066c-320">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="e066c-320">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="e066c-321">Kombinieren von sozialen und lokalen Anmeldekonten</span><span class="sxs-lookup"><span data-stu-id="e066c-321">Combine social and local login accounts</span></span>

<span data-ttu-id="e066c-322">Zum Durchführen dieses Abschnitts müssen Sie zuerst einen externen Authentifizierungs Anbieter aktivieren.</span><span class="sxs-lookup"><span data-stu-id="e066c-322">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="e066c-323">Weitere Informationen finden Sie unter [Authentifizierung für Facebook, Google und externe Anbieter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="e066c-323">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="e066c-324">Sie können lokale und soziale Konten kombinieren, indem Sie auf Ihren e-Mail-Link klicken.</span><span class="sxs-lookup"><span data-stu-id="e066c-324">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="e066c-325">In der folgenden Sequenz wird " RickAndMSFT@gmail.com " zuerst als lokale Anmeldung erstellt. Sie können das Konto jedoch zuerst als Social Login (Social Login) erstellen und dann einen lokalen Anmelde Namen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="e066c-325">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webanwendung: RickAndMSFT@gmail.com Benutzer authentifiziert](accconfirm/_static/rick.png)

<span data-ttu-id="e066c-327">Klicken Sie auf den Link **Verwalten** .</span><span class="sxs-lookup"><span data-stu-id="e066c-327">Click on the **Manage** link.</span></span> <span data-ttu-id="e066c-328">Beachten Sie die 0 (null) externen (Anmeldungen), die diesem Konto zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="e066c-328">Note the 0 external (social logins) associated with this account.</span></span>

![Ansicht verwalten](accconfirm/_static/manage.png)

<span data-ttu-id="e066c-330">Klicken Sie auf den Link zu einem anderen Anmeldedienst, und akzeptieren Sie die APP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="e066c-330">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="e066c-331">In der folgenden Abbildung ist Facebook der externe Authentifizierungs Anbieter:</span><span class="sxs-lookup"><span data-stu-id="e066c-331">In the following image, Facebook is the external authentication provider:</span></span>

![Verwalten der Ansicht externer Anmeldungen mit Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="e066c-333">Die beiden Konten wurden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="e066c-333">The two accounts have been combined.</span></span> <span data-ttu-id="e066c-334">Sie können sich mit einem der beiden Konten anmelden.</span><span class="sxs-lookup"><span data-stu-id="e066c-334">You are able to sign in with either account.</span></span> <span data-ttu-id="e066c-335">Möglicherweise möchten Sie, dass Ihre Benutzer lokale Konten hinzufügen, falls der Authentifizierungsdienst für soziale Anmelde Informationen ausgefallen ist oder Sie den Zugriff auf Ihr Social Media-Konto verlieren.</span><span class="sxs-lookup"><span data-stu-id="e066c-335">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="e066c-336">Aktivieren der Konto Bestätigung nach einem Standort Benutzer</span><span class="sxs-lookup"><span data-stu-id="e066c-336">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="e066c-337">Das Aktivieren der Konto Bestätigung auf einem Standort mit Benutzern sperrt alle vorhandenen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="e066c-337">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="e066c-338">Vorhandene Benutzer werden gesperrt, da ihre Konten nicht bestätigt werden.</span><span class="sxs-lookup"><span data-stu-id="e066c-338">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="e066c-339">Verwenden Sie einen der folgenden Ansätze, um die vorhandene Benutzer Sperre zu umgehen:</span><span class="sxs-lookup"><span data-stu-id="e066c-339">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="e066c-340">Aktualisieren Sie die Datenbank, um alle vorhandenen Benutzer als bestätigt zu markieren.</span><span class="sxs-lookup"><span data-stu-id="e066c-340">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="e066c-341">Vorhandene Benutzer bestätigen.</span><span class="sxs-lookup"><span data-stu-id="e066c-341">Confirm existing users.</span></span> <span data-ttu-id="e066c-342">Beispiel: Senden von e-Mails mit Bestätigungs Links per Batch.</span><span class="sxs-lookup"><span data-stu-id="e066c-342">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
