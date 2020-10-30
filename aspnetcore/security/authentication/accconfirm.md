---
title: Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.net Core-App mit e-Mail-Bestätigung und Kenn Wort Zurücksetzung
ms.author: riande
ms.date: 03/11/2019
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
uid: security/authentication/accconfirm
ms.openlocfilehash: 91148c67d5dc0bf97e2f926f50dcff5dd0708f4b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052317"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)und [Joe Audette](https://twitter.com/joeaudette)

Dieses Tutorial zeigt, wie Sie eine ASP.net Core-App mit e-Mail-Bestätigung und Kenn Wort Zurücksetzung erstellen. Dieses Tutorial ist **kein** Thema. Sie sollten sich mit folgenden Aktionen vertraut machen:

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [Authentifizierung](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a>Voraussetzungen

[.NET Core 3.0 SDK oder höher](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>Erstellen und Testen einer Web-App mit Authentifizierung

Führen Sie die folgenden Befehle aus, um eine Web-App mit Authentifizierung zu erstellen.

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

Führen Sie die APP aus, wählen Sie den Link **registrieren** aus, und registrieren Sie einen Benutzer. Nach der Registrierung werden Sie zur Seite an weitergeleitet, `/Identity/Account/RegisterConfirmation` die einen Link zum Simulieren einer e-Mail-Bestätigung enthält:

* Wählen Sie den `Click here to confirm your account` Link aus.
* Wählen Sie den **Anmelde** Link aus, und melden Sie sich mit denselben Anmelde Informationen an.
* Wählen Sie den `Hello YourEmail@provider.com!` Link aus, der Sie zur Seite umleitet `/Identity/Account/Manage/PersonalData` .
* Wählen Sie auf der linken Seite die Registerkarte **persönliche Daten** aus, und klicken Sie dann auf **Löschen** .

### <a name="configure-an-email-provider"></a>Konfigurieren eines e-Mail-Anbieters

In diesem Tutorial wird [sendgrid](https://sendgrid.com) zum Senden von e-Mails verwendet. Sie benötigen ein sendgrid-Konto und einen Schlüssel zum Senden von e-Mails. Sie können andere e-Mail-Anbieter verwenden. Es wird empfohlen, sendgrid oder einen anderen e-Mail-Dienst zum Senden von e-Mails zu verwenden SMTP ist schwierig zu sichern und ordnungsgemäß eingerichtet.

Das sendgrid-Konto erfordert möglicherweise das [Hinzufügen eines Absenders](https://sendgrid.com/docs/ui/sending-email/senders/).

Erstellen Sie eine Klasse zum Abrufen des Schlüssels für sichere e-Mails. Erstellen Sie für dieses Beispiel *Dienste/authmessagesenderoptions. cs* :

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Konfigurieren von sendgrid-Benutzer Geheimnissen

Legen Sie `SendGridUser` und `SendGridKey` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest. Zum Beispiel:

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Unter Windows speichert Secret Manager Schlüssel-Wert-Paare in einer *secrets.js* in der Datei im `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.

Der Inhalt des *secrets.jsin* der Datei ist nicht verschlüsselt. Das folgende Markup zeigt die *secrets.jsfür* die Datei. Der `SendGridKey` Wert wurde entfernt.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Weitere Informationen finden Sie im [options Muster](xref:fundamentals/configuration/options) und in der [Konfiguration](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Installieren von sendgrid

In diesem Tutorial wird gezeigt, wie Sie e-Mail-Benachrichtigungen über [sendgrid](https://sendgrid.com/)hinzufügen, aber Sie können e-Mails mithilfe von SMTP und anderen Mechanismen senden.

Installieren Sie das `SendGrid` nuget-Paket:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Geben Sie in der Konsole den folgenden Befehl ein:

```dotnetcli
dotnet add package SendGrid
```

---

Weitere Informationen zur Registrierung für ein kostenloses sendgrid-Konto finden [Sie unter Get Started with sendgrid](https://sendgrid.com/free/) .

### <a name="implement-iemailsender"></a>Implementieren von iemailsender

Erstellen Sie zum Implementieren von `IEmailSender` *Services/emailsender. cs* mit Code, der dem folgenden ähnelt:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Konfigurieren des Starts zur Unterstützung von e-Mails

Fügen Sie der- `ConfigureServices` Methode in der Datei *Startup.cs* den folgenden Code hinzu:

* Fügen Sie `EmailSender` als vorübergehenden Dienst hinzu.
* Registrieren Sie die `AuthMessageSenderOptions` Konfigurations Instanz.

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a>Gerüst Register Bestätigung

Befolgen Sie die Anweisungen für [ Identity Gerüstbau](xref:security/authentication/scaffold-identity) und Gerüstbau `RegisterConfirmation` .

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a>Registrieren, bestätigen der e-Mail und Zurücksetzen des Kennworts

Führen Sie die Web-App aus, und testen Sie den Ablauf der Konto Bestätigung und Kenn Wort Wiederherstellung.

* Ausführen der APP und Registrieren eines neuen Benutzers
* Überprüfen Sie Ihre e-Mail auf den Link zur Konto Bestätigung. Siehe [Debuggen von e-Mails](#debug) , wenn Sie die e-Mail nicht erhalten.
* Klicken Sie auf den Link, um Ihre e-Mail zu bestätigen.
* Melden Sie sich mit Ihrer e-Mail und dem Kennwort an.
* Melden Sie sich ab.

### <a name="test-password-reset"></a>Kenn Wort Zurücksetzung testen

* Wenn Sie angemeldet sind, wählen Sie **Abmelden aus** .
* Wählen Sie den Link **Anmelden aus** , und wählen Sie den Link **Kennwort vergessen?** aus.
* Geben Sie die e-Mail ein, die Sie zum Registrieren des Kontos verwendet haben.
* Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts wird gesendet. Überprüfen Sie Ihre e-Mail, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts Nachdem das Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrer e-Mail-Adresse und dem neuen Kennwort anmelden.

<a name="resend"></a>

## <a name="resend-email-confirmation"></a>E-Mail-Bestätigung erneut senden

Wählen Sie in ASP.net Core 5,0 und höher den Link **e-Mail-Bestätigung erneut senden** auf der **Anmelde** Seite aus.

### <a name="change-email-and-activity-timeout"></a>E-Mail-und Aktivitäts Timeout ändern

Das standardmäßige Inaktivitäts Timeout beträgt 14 Tage. Mit dem folgenden Code wird das Inaktivitäts Timeout auf 5 Tage festgelegt:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Alle Datenschutz Token-Lebensdauer ändern

Der folgende Code ändert den Timeout Zeitraum für alle Token für die Datenschutz Token in 3 Stunden:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

Die integrierten Identity Benutzer Token (siehe [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben ein [Timeout von einem Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Lebensdauer von e-Mail-Token

Die standardmäßige tokengültigkeits Dauer der [ Identity Benutzer Token](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [ein Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). In diesem Abschnitt wird gezeigt, wie Sie die Lebensdauer von e-Mail-

Fügen Sie einen benutzerdefinierten [dataprotectortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und Folgendes hinzu <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Fügen Sie dem Dienst Container den benutzerdefinierten Anbieter hinzu:

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a>E-Mail Debuggen

Wenn Sie keine e-Mail mehr erhalten können:

* Legen Sie einen Haltepunkt fest `EmailSender.Execute` , um zu überprüfen, ob `SendGridClient.SendEmailAsync` aufgerufen wird
* Erstellen [Sie eine Konsolen-App zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mithilfe eines ähnlichen Codes an `EmailSender.Execute` .
* Lesen Sie die Seite [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Überprüfen Sie Ihren Spam-Ordner.
* Probieren Sie einen anderen e-Mail-Alias für einen anderen e-Mail-Anbieter aus (Microsoft, Yahoo, Gmail usw.)
* Senden Sie an verschiedene e-Mail-Konten.

**Eine bewährte Sicherheits** Maßnahme besteht darin, **keine** Produktionsgeheimnisse in Test-und Entwicklungsumgebungen zu verwenden. Wenn Sie die app in Azure veröffentlichen, legen Sie die sendgrid-Geheimnisse als Anwendungseinstellungen im Azure-Web-App-Portal fest. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

## <a name="combine-social-and-local-login-accounts"></a>Kombinieren von sozialen und lokalen Anmeldekonten

Zum Durchführen dieses Abschnitts müssen Sie zuerst einen externen Authentifizierungs Anbieter aktivieren. Weitere Informationen finden Sie unter [Authentifizierung für Facebook, Google und externe Anbieter](xref:security/authentication/social/index).

Sie können lokale und soziale Konten kombinieren, indem Sie auf Ihren e-Mail-Link klicken. In der folgenden Sequenz wird " RickAndMSFT@gmail.com " zuerst als lokale Anmeldung erstellt. Sie können das Konto jedoch zuerst als Social Login (Social Login) erstellen und dann einen lokalen Anmelde Namen hinzufügen.

![Webanwendung: RickAndMSFT@gmail.com Benutzer authentifiziert](accconfirm/_static/rick.png)

Klicken Sie auf den Link **Verwalten** . Beachten Sie die 0 (null) externen (Anmeldungen), die diesem Konto zugeordnet sind.

![Ansicht verwalten](accconfirm/_static/manage.png)

Klicken Sie auf den Link zu einem anderen Anmeldedienst, und akzeptieren Sie die APP-Anforderungen. In der folgenden Abbildung ist Facebook der externe Authentifizierungs Anbieter:

![Verwalten der Ansicht externer Anmeldungen mit Facebook](accconfirm/_static/fb.png)

Die beiden Konten wurden kombiniert. Sie können sich mit einem der beiden Konten anmelden. Möglicherweise möchten Sie, dass Ihre Benutzer lokale Konten hinzufügen, falls der Authentifizierungsdienst für soziale Anmelde Informationen ausgefallen ist oder Sie den Zugriff auf Ihr Social Media-Konto verlieren.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Aktivieren der Konto Bestätigung nach einem Standort Benutzer

Das Aktivieren der Konto Bestätigung auf einem Standort mit Benutzern sperrt alle vorhandenen Benutzer. Vorhandene Benutzer werden gesperrt, da ihre Konten nicht bestätigt werden. Verwenden Sie einen der folgenden Ansätze, um die vorhandene Benutzer Sperre zu umgehen:

* Aktualisieren Sie die Datenbank, um alle vorhandenen Benutzer als bestätigt zu markieren.
* Vorhandene Benutzer bestätigen. Beispiel: Senden von e-Mails mit Bestätigungs Links per Batch.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a>Voraussetzungen

[.Net Core 2,2 SDK oder höher](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a>Erstellen einer Web-App und eines Gerüsts Identity

Führen Sie die folgenden Befehle aus, um eine Web-App mit Authentifizierung zu erstellen.

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
> Wenn <xref:Microsoft.AspNetCore.Identity.PasswordOptions> in konfiguriert `Startup.ConfigureServices` ist, ist möglicherweise die- [ `[StringLength]` Attribut](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) Konfiguration für die- `Password` Eigenschaft auf den Seiten mit einem Gerüst erforderlich Identity . Eine- `InputModel` `Password` Eigenschaft wurde nach dem `Areas/Identity/Pages/Account/Register.cshtml.cs` Gerüstbau in der Datei gefunden Identity .

## <a name="test-new-user-registration"></a>Neue Benutzerregistrierung testen

Führen Sie die APP aus, wählen Sie den Link **registrieren** aus, und registrieren Sie einen Benutzer. An diesem Punkt wird nur die e-Mail-Überprüfung mit dem- [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) Attribut durchzuführen. Nachdem Sie die Registrierung übermittelt haben, werden Sie bei der App angemeldet. Später in diesem Tutorial wird der Code aktualisiert, sodass neue Benutzer sich erst anmelden können, wenn Ihre e-Mail-Nachricht überprüft wurde.

[!INCLUDE[](~/includes/view-identity-db.md)]

Beachten Sie, dass das Feld der Tabelle `EmailConfirmed` ist `False` .

Sie sollten diese e-Mail im nächsten Schritt erneut verwenden, wenn die APP eine Bestätigungs-e-Mail sendet. Klicken Sie mit der rechten Maustaste auf die Zeile, und wählen Sie **Löschen** . Wenn Sie den e-Mail-Alias löschen, wird dies in den folgenden Schritten vereinfacht.

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>Bestätigung per e-Mail erforderlich

Es wird empfohlen, die e-Mail-Adresse einer neuen Benutzerregistrierung zu bestätigen. Mithilfe der e-Mail-Bestätigung können Sie sicherstellen, dass Sie keine andere Identität annehmen (d. h., Sie haben sich nicht bei einer anderen e-Mail registriert). Angenommen, Sie hatten ein Diskussionsforum, und Sie wollten verhindern, dass sich " yli@example.com " als "" registriert nolivetto@contoso.com . Ohne e-Mail-Bestätigung kann " nolivetto@contoso.com " unerwünschte e-Mails von Ihrer APP erhalten. Angenommen, der Benutzer hat sich versehentlich als " ylo@example.com " registriert und die falsche Schreibweise von "Yli" nicht bemerkt. Sie sind nicht in der Lage, die Kenn Wort Wiederherstellung zu verwenden, da die APP nicht über die richtige e-Mail verfügt E-Mail-Bestätigung bietet eingeschränkten Schutz vor Bots. E-Mail-Bestätigung bietet keinen Schutz vor böswilligen Benutzern mit vielen e-Mail-Konten

In der Regel möchten Sie, dass neue Benutzerdaten an Ihrer Website veröffentlichen, bevor Sie eine bestätigte e-Mail-Adresse haben.

Aktualisieren `Startup.ConfigureServices`  Sie, um eine bestätigte e-Mail anzufordern:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` verhindert, dass registrierte Benutzer sich anmelden, bis Ihre e-Mail-Nachricht bestätigt wird.

### <a name="configure-email-provider"></a>E-Mail-Anbieter

In diesem Tutorial wird [sendgrid](https://sendgrid.com) zum Senden von e-Mails verwendet. Sie benötigen ein sendgrid-Konto und einen Schlüssel zum Senden von e-Mails. Sie können andere e-Mail-Anbieter verwenden. ASP.net Core 2. x umfasst `System.Net.Mail` , das Ihnen ermöglicht, e-Mails von Ihrer APP zu senden. Es wird empfohlen, sendgrid oder einen anderen e-Mail-Dienst zum Senden von e-Mails zu verwenden SMTP ist schwierig zu sichern und ordnungsgemäß eingerichtet.

Erstellen Sie eine Klasse zum Abrufen des Schlüssels für sichere e-Mails. Erstellen Sie für dieses Beispiel *Dienste/authmessagesenderoptions. cs* :

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>Konfigurieren von sendgrid-Benutzer Geheimnissen

Legen Sie `SendGridUser` und `SendGridKey` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest. Zum Beispiel:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

Unter Windows speichert Secret Manager Schlüssel-Wert-Paare in einer *secrets.js* in der Datei im `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.

Der Inhalt des *secrets.jsin* der Datei ist nicht verschlüsselt. Das folgende Markup zeigt die *secrets.jsfür* die Datei. Der `SendGridKey` Wert wurde entfernt.

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

Weitere Informationen finden Sie im [options Muster](xref:fundamentals/configuration/options) und in der [Konfiguration](xref:fundamentals/configuration/index).

### <a name="install-sendgrid"></a>Installieren von sendgrid

In diesem Tutorial wird gezeigt, wie Sie e-Mail-Benachrichtigungen über [sendgrid](https://sendgrid.com/)hinzufügen, aber Sie können e-Mails mithilfe von SMTP und anderen Mechanismen senden.

Installieren Sie das `SendGrid` nuget-Paket:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Geben Sie in der Konsole den folgenden Befehl ein:

```dotnetcli
dotnet add package SendGrid
```

---

Weitere Informationen zur Registrierung für ein kostenloses sendgrid-Konto finden [Sie unter Get Started with sendgrid](https://sendgrid.com/free/) .

### <a name="implement-iemailsender"></a>Implementieren von iemailsender

Erstellen Sie zum Implementieren von `IEmailSender` *Services/emailsender. cs* mit Code, der dem folgenden ähnelt:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>Konfigurieren des Starts zur Unterstützung von e-Mails

Fügen Sie der- `ConfigureServices` Methode in der Datei *Startup.cs* den folgenden Code hinzu:

* Fügen Sie `EmailSender` als vorübergehenden Dienst hinzu.
* Registrieren Sie die `AuthMessageSenderOptions` Konfigurations Instanz.

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>Aktivieren der Konto Bestätigung und Kenn Wort Wiederherstellung

Die Vorlage enthält den Code für die Konto Bestätigung und Kenn Wort Wiederherstellung. Suchen Sie die `OnPostAsync` -Methode in " *Areas/ Identity /pages/Account/Register.cshtml.cs* ".

Verhindern Sie, dass neu registrierte Benutzer automatisch angemeldet werden, indem Sie die folgende Zeile auskommentieren:

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

Die gesamte-Methode wird angezeigt, wenn die geänderte Zeile hervorgehoben ist:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>Registrieren, bestätigen der e-Mail und Zurücksetzen des Kennworts

Führen Sie die Web-App aus, und testen Sie den Ablauf der Konto Bestätigung und Kenn Wort Wiederherstellung.

* Ausführen der APP und Registrieren eines neuen Benutzers
* Überprüfen Sie Ihre e-Mail auf den Link zur Konto Bestätigung. Siehe [Debuggen von e-Mails](#debug) , wenn Sie die e-Mail nicht erhalten.
* Klicken Sie auf den Link, um Ihre e-Mail zu bestätigen.
* Melden Sie sich mit Ihrer e-Mail und dem Kennwort an.
* Melden Sie sich ab.

### <a name="view-the-manage-page"></a>Anzeigen der Seite "verwalten"

Wählen Sie Ihren Benutzernamen im Browser aus: ![ Browserfenster mit Benutzername](accconfirm/_static/un.png)

Die Seite verwalten wird angezeigt, und die Registerkarte **Profil** ist ausgewählt. Die **e-Mail** zeigt ein Kontrollkästchen an, das anzeigt, dass die e-Mail bestätigt wurde.

### <a name="test-password-reset"></a>Kenn Wort Zurücksetzung testen

* Wenn Sie angemeldet sind, wählen Sie **Abmelden aus** .
* Wählen Sie den Link **Anmelden aus** , und wählen Sie den Link **Kennwort vergessen?** aus.
* Geben Sie die e-Mail ein, die Sie zum Registrieren des Kontos verwendet haben.
* Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts wird gesendet. Überprüfen Sie Ihre e-Mail, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts Nachdem das Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrer e-Mail-Adresse und dem neuen Kennwort anmelden.

## <a name="change-email-and-activity-timeout"></a>E-Mail-und Aktivitäts Timeout ändern

Das standardmäßige Inaktivitäts Timeout beträgt 14 Tage. Mit dem folgenden Code wird das Inaktivitäts Timeout auf 5 Tage festgelegt:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>Alle Datenschutz Token-Lebensdauer ändern

Der folgende Code ändert den Timeout Zeitraum für alle Token für die Datenschutz Token in 3 Stunden:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

Die integrierten Identity Benutzer Token (siehe [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben ein [Timeout von einem Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).

### <a name="change-the-email-token-lifespan"></a>Lebensdauer von e-Mail-Token

Die standardmäßige tokengültigkeits Dauer der [ Identity Benutzer Token](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [ein Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs). In diesem Abschnitt wird gezeigt, wie Sie die Lebensdauer von e-Mail-

Fügen Sie einen benutzerdefinierten [dataprotectortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und Folgendes hinzu <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

Fügen Sie dem Dienst Container den benutzerdefinierten Anbieter hinzu:

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>E-Mail-Bestätigung erneut senden

Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/5410).

<a name="debug"></a>

### <a name="debug-email"></a>E-Mail Debuggen

Wenn Sie keine e-Mail mehr erhalten können:

* Legen Sie einen Haltepunkt fest `EmailSender.Execute` , um zu überprüfen, ob `SendGridClient.SendEmailAsync` aufgerufen wird
* Erstellen [Sie eine Konsolen-App zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mithilfe eines ähnlichen Codes an `EmailSender.Execute` .
* Lesen Sie die Seite [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) .
* Überprüfen Sie Ihren Spam-Ordner.
* Probieren Sie einen anderen e-Mail-Alias für einen anderen e-Mail-Anbieter aus (Microsoft, Yahoo, Gmail usw.)
* Senden Sie an verschiedene e-Mail-Konten.

**Eine bewährte Sicherheits** Maßnahme besteht darin, **keine** Produktionsgeheimnisse in Test-und Entwicklungsumgebungen zu verwenden. Wenn Sie die app in Azure veröffentlichen, können Sie die sendgrid-Geheimnisse als Anwendungseinstellungen im Azure-Web-App-Portal festlegen. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.

## <a name="combine-social-and-local-login-accounts"></a>Kombinieren von sozialen und lokalen Anmeldekonten

Zum Durchführen dieses Abschnitts müssen Sie zuerst einen externen Authentifizierungs Anbieter aktivieren. Weitere Informationen finden Sie unter [Authentifizierung für Facebook, Google und externe Anbieter](xref:security/authentication/social/index).

Sie können lokale und soziale Konten kombinieren, indem Sie auf Ihren e-Mail-Link klicken. In der folgenden Sequenz wird " RickAndMSFT@gmail.com " zuerst als lokale Anmeldung erstellt. Sie können das Konto jedoch zuerst als Social Login (Social Login) erstellen und dann einen lokalen Anmelde Namen hinzufügen.

![Webanwendung: RickAndMSFT@gmail.com Benutzer authentifiziert](accconfirm/_static/rick.png)

Klicken Sie auf den Link **Verwalten** . Beachten Sie die 0 (null) externen (Anmeldungen), die diesem Konto zugeordnet sind.

![Ansicht verwalten](accconfirm/_static/manage.png)

Klicken Sie auf den Link zu einem anderen Anmeldedienst, und akzeptieren Sie die APP-Anforderungen. In der folgenden Abbildung ist Facebook der externe Authentifizierungs Anbieter:

![Verwalten der Ansicht externer Anmeldungen mit Facebook](accconfirm/_static/fb.png)

Die beiden Konten wurden kombiniert. Sie können sich mit einem der beiden Konten anmelden. Möglicherweise möchten Sie, dass Ihre Benutzer lokale Konten hinzufügen, falls der Authentifizierungsdienst für soziale Anmelde Informationen ausgefallen ist oder Sie den Zugriff auf Ihr Social Media-Konto verlieren.

## <a name="enable-account-confirmation-after-a-site-has-users"></a>Aktivieren der Konto Bestätigung nach einem Standort Benutzer

Das Aktivieren der Konto Bestätigung auf einem Standort mit Benutzern sperrt alle vorhandenen Benutzer. Vorhandene Benutzer werden gesperrt, da ihre Konten nicht bestätigt werden. Verwenden Sie einen der folgenden Ansätze, um die vorhandene Benutzer Sperre zu umgehen:

* Aktualisieren Sie die Datenbank, um alle vorhandenen Benutzer als bestätigt zu markieren.
* Vorhandene Benutzer bestätigen. Beispiel: Senden von e-Mails mit Bestätigungs Links per Batch.

::: moniker-end
