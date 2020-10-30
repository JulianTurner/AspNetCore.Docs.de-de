---
title: Zweistufige Authentifizierung mit SMS in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie die zweistufige Authentifizierung (2FA) mit einer ASP.net Core-App einrichten.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
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
uid: security/authentication/2fa
ms.openlocfilehash: 1ee9e656c2e631c9b5588149e0a75e07108baff1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051264"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Zweistufige Authentifizierung mit SMS in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Schweizer-Entwickler](https://github.com/Swiss-Devs)

>[!WARNING]
> Authentifikator-apps mit zweistufiger Authentifizierung (Two-Factor Authentication, 2FA). dabei handelt es sich um einen zeitbasierten einmaligen Kenn Wort Algorithmus (TOTP), der von der Branche empfohlen wird. die 2FA-Verwendung von TOTP wird für SMS 2FA bevorzugt. Weitere Informationen finden Sie unter [Aktivieren der QR-Code Generierung für TOTP Authenticator-apps in ASP.net Core](xref:security/authentication/identity-enable-qrcodes) für ASP.net Core 2,0 und höher.

In diesem Tutorial wird gezeigt, wie Sie die zweistufige Authentifizierung (2FA) mithilfe von SMS einrichten. Für [twilio](https://www.twilio.com/) und [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)werden Anweisungen erteilt, aber Sie können auch einen beliebigen anderen SMS-Anbieter verwenden. Bevor Sie mit diesem Tutorial beginnen, wird empfohlen, die [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm) abzuschließen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA) [Herunterladen von](xref:index#how-to-download-a-sample)

## <a name="create-a-new-aspnet-core-project"></a>Erstellen eines neuen ASP.NET Core-Projekts

Erstellen Sie eine neue ASP.net Core Web-App mit dem Namen `Web2FA` mit einzelnen Benutzerkonten. Befolgen Sie die Anweisungen unter <xref:security/enforcing-ssl> , um HTTPS einzurichten und anzufordern.

### <a name="create-an-sms-account"></a>Erstellen eines SMS-Kontos

Erstellen Sie ein SMS-Konto, z. b. aus [twilio](https://www.twilio.com/) oder [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Notieren Sie die Anmelde Informationen für die Authentifizierung (für twilio: AccountSid und AuthToken für aspsms: UserKey und Password).

#### <a name="figuring-out-sms-provider-credentials"></a>Ermitteln der Anmelde Informationen für den SMS-Anbieter

**Twilio**

Kopieren Sie die **Konto-SID** und das Authentifizierungs **Token** auf der Registerkarte Dashboard Ihres twilio-Kontos.

**Aspsms:**

Navigieren Sie in Ihren Kontoeinstellungen zu **UserKey** , und kopieren Sie es mit Ihrem **Kennwort** .

Diese Werte werden später in mit dem Secret-Manager-Tool in den Schlüsseln `SMSAccountIdentification` und gespeichert `SMSAccountPassword` .

#### <a name="specifying-senderid--originator"></a>Angeben von SenderID/Absender

**Twilio:** Kopieren Sie Ihre twilio- **Telefonnummer** auf der Registerkarte Zahlen.

**Aspsms:** Entsperren Sie einen oder mehrere Originatoren im Menü Unlock-Absender, oder wählen Sie einen alphanumerischen Absender aus (wird nicht von allen Netzwerken unterstützt).

Dieser Wert wird später mit dem Secret-Manager-Tool im Schlüssel gespeichert `SMSAccountFrom` .

### <a name="provide-credentials-for-the-sms-service"></a>Angeben von Anmelde Informationen für den SMS-Dienst

Wir verwenden das [options Muster](xref:fundamentals/configuration/options) für den Zugriff auf das Benutzerkonto und die Schlüssel Einstellungen.

* Erstellen Sie eine Klasse, um den sicheren SMS-Schlüssel abzurufen. In diesem Beispiel wird die `SMSoptions` -Klasse in der Datei *Services/smsoptions. cs* erstellt.

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Legen Sie `SMSAccountIdentification` `SMSAccountPassword` und `SMSAccountFrom` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest. Zum Beispiel:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* Fügen Sie das nuget-Paket für den SMS-Anbieter hinzu. Führen Sie in der Paket-Manager-Konsole (PMC) Folgendes aus:

**Twilio**

`Install-Package Twilio`

**Aspsms:**

`Install-Package ASPSMS`

* Fügen Sie Code in der Datei " *Services/messageservices. cs* " hinzu, um SMS zu aktivieren. Verwenden Sie entweder den twilio-oder den ASPSMS-Abschnitt:

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**Aspsms:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Zu verwendenden Start konfigurieren `SMSoptions`

Fügen Sie dem `SMSoptions` Dienst Container in der- `ConfigureServices` Methode in der *Startup.cs* hinzu:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Aktivieren Sie die zweistufige Authentifizierung.

Öffnen Sie die Ansichts Datei *views/Manage/Index. cshtml* Razor , und entfernen Sie die Kommentarzeichen (sodass kein Markup auskommentiert ist).

## <a name="log-in-with-two-factor-authentication"></a>Anmelden mit zweistufiger Authentifizierung

* Ausführen der APP und Registrieren eines neuen Benutzers

![Ansicht "Webanwendungs Register" in Microsoft Edge geöffnet](2fa/_static/login2fa1.png)

* Tippen Sie auf Ihren Benutzernamen, der die `Index` Aktionsmethode in Manage Controller aktiviert. Tippen Sie dann auf den Link Telefonnummer **Hinzufügen** .

![Ansicht verwalten: Tippen Sie auf den Link "hinzufügen".](2fa/_static/login2fa2.png)

* Fügen Sie eine Telefonnummer hinzu, die den Überprüfungs Code empfängt, und tippen **Sie auf Überprüfungs Code senden** .

![Seite "Telefonnummer hinzufügen"](2fa/_static/login2fa3.png)

* Sie erhalten eine Textnachricht mit dem Überprüfungs Code. EINGABETASTE **Submit**

![Seite "Telefonnummer überprüfen"](2fa/_static/login2fa4.png)

Wenn Sie keine SMS erhalten, finden Sie weitere Informationen auf der twilio-Protokoll Seite.

* In der Ansicht verwalten wird angezeigt, dass Ihre Telefonnummer erfolgreich hinzugefügt wurde.

![Ansicht verwalten-die Telefonnummer wurde erfolgreich hinzugefügt.](2fa/_static/login2fa5.png)

* Tippen Sie auf **aktivieren** , um die zweistufige Authentifizierung zu aktivieren.

![Ansicht verwalten: Aktivieren der zweistufigen Authentifizierung](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Testen der zweistufigen Authentifizierung

* Melden Sie sich ab.

* Melden Sie sich an.

* Das Benutzerkonto hat die zweistufige Authentifizierung aktiviert, sodass Sie den zweiten Authentifizierungs Faktor bereitstellen müssen. In diesem Tutorial haben Sie die Telefon Überprüfung aktiviert. Die integrierten Vorlagen ermöglichen Ihnen auch das Einrichten von e-Mails als zweiten Faktor. Sie können weitere zweite Faktoren für die Authentifizierung einrichten, z. b. QR-Codes. Tippen Sie auf **senden** .

![Überprüfungs Code Ansicht senden](2fa/_static/login2fa7.png)

* Geben Sie den Code ein, den Sie in der SMS-Nachricht erhalten.

* Wenn Sie auf das Kontrollkästchen **diesen Browser speichern** klicken, werden Sie von der Verwendung von 2FA zum Anmelden aufgefordert, wenn Sie das gleiche Gerät und den gleichen Browser verwenden. Durch das Aktivieren von 2FA und das Klicken auf **Speichern dieses Browsers** erhalten Sie einen starken 2FA-Schutz vor böswilligen Benutzern, die versuchen, auf Ihr Konto zuzugreifen, solange Sie keinen Zugriff auf Ihr Gerät haben. Hierfür können Sie ein beliebiges privates Gerät verwenden, das Sie regelmäßig verwenden. Wenn Sie  **diesen Browser merken** , erhalten Sie die zusätzliche Sicherheit von 2FA von Geräten, die Sie nicht regelmäßig verwenden, und Sie erhalten die Möglichkeit, die 2FA nicht auf Ihren eigenen Geräten zu durchlaufen.

![Ansicht überprüfen](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Konto Sperre zum Schutz vor Brute-Force-Angriffen

Die Kontosperrung wird mit 2FA empfohlen. Sobald sich ein Benutzer über ein lokales Konto oder ein Social Media-Konto anmeldet, wird jeder fehlgeschlagene Versuch bei "2FA" gespeichert. Wenn die maximale Anzahl fehlerhafter Zugriffsversuche erreicht ist, wird der Benutzer gesperrt (Standard: 5 Minuten Sperre nach fünf fehlgeschlagenen Zugriffsversuchen). Bei erfolgreicher Authentifizierung wird die Anzahl fehlerhafter Zugriffsversuche zurückgesetzt, und die Uhr wird zurückgesetzt. Die maximalen fehlgeschlagenen Zugriffsversuche und Sperr Zeit können mit " [maxfailedaccessversuchs](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) " und " [defaultlockouttimespan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)" festgelegt werden. Nachfolgend wird die Kontosperrung 10 Minuten nach 10 fehlgeschlagenen Zugriffsversuchen konfiguriert:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Vergewissern Sie sich, dass [passwordsigninasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) auf festgelegt ist `lockoutOnFailure` `true` :

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
