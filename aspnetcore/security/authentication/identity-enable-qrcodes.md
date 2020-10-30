---
title: Aktivieren der QR-Code Generierung für TOTP Authenticator-apps in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie die QR-Codegenerierung für TOTP Authenticator-Apps aktivieren, die mit ASP.net Core zweistufigen Authentifizierung funktionieren.
ms.author: riande
ms.date: 08/14/2018
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
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: b778e7238911ec9966edf7f0f7becd113b1e197a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060832"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>Aktivieren der QR-Code Generierung für TOTP Authenticator-apps in ASP.net Core

::: moniker range="<= aspnetcore-2.0"

Für QR-Codes ist ASP.net Core 2,0 oder höher erforderlich.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

ASP.net Core wird mit Unterstützung für authentifizierungsanwendungen für die individuelle Authentifizierung ausgeliefert. Authentifikator-apps mit zweistufiger Authentifizierung (Two-Factor Authentication, 2FA). dabei handelt es sich um einen zeitbasierten einmaligen Kenn Wort Algorithmus (TOTP), der von der Branche empfohlen wird. die 2FA-Verwendung von TOTP wird für SMS 2FA bevorzugt. Eine Authentifikator-App bietet einen 6-bis achtstelligen Code, den Benutzer nach der Bestätigung Ihres Benutzernamens und Kennworts eingeben müssen. In der Regel wird eine Authentifikator-App auf einem Smartphone installiert.

Die ASP.net Core Web-App-Vorlagen unterstützen Authentifikatoren, bieten jedoch keine Unterstützung für die QRCode-Generierung. QRCode-Generatoren vereinfachen das Einrichten von 2FA. Dieses Dokument führt Sie durch das Hinzufügen der [QR-Code](https://wikipedia.org/wiki/QR_code) Generierung zur Seite "2FA-Konfiguration".

Die zweistufige Authentifizierung erfolgt nicht mithilfe eines externen Authentifizierungs Anbieters, z. b. [Google](xref:security/authentication/google-logins) oder [Facebook](xref:security/authentication/facebook-logins). Externe Anmeldungen werden durch einen beliebigen Mechanismus geschützt, den der externe Anmelde Anbieter bereitstellt. Beachten Sie, dass der [Microsoft](xref:security/authentication/microsoft-logins) -Authentifizierungs Anbieter beispielsweise einen Hardwareschlüssel oder einen anderen 2FA-Ansatz erfordert. Wenn die Standardvorlagen "local" 2FA erzwingen, müssten Benutzer zwei 2FA-Ansätze erfüllen. Dies ist kein häufig verwendetes Szenario.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>Hinzufügen von QR-Codes zur Seite "2FA-Konfiguration"

In diesen Anweisungen wird *qrcode.js* aus dem Repository verwendet https://davidshimjs.github.io/qrcodejs/ .

* Laden Sie die [ JavaScript-Bibliothekqrcode.js](https://davidshimjs.github.io/qrcodejs/) in den `wwwroot\lib` Ordner in Ihrem Projekt herunter.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* Befolgen Sie die Anweisungen in [ Identity Gerüstbau](xref:security/authentication/scaffold-identity) , um */Areas/ Identity /pages/Account/Manage/EnableAuthenticator.cshtml* zu generieren.
* Suchen Sie in */Areas/ Identity /pages/Account/Manage/EnableAuthenticator.cshtml* den `Scripts` Abschnitt am Ende der Datei:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* Suchen Sie in *pages/Account/Manage/enableauthenticator. cshtml* ( Razor Pages) oder *views/Manage/enableauthenticator. cshtml* (MVC) den `Scripts` Abschnitt am Ende der Datei:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* Aktualisieren Sie den `Scripts` Abschnitt, um einen Verweis auf die `qrcodejs` hinzugefügte Bibliothek hinzuzufügen, und einen-Befehl zum Generieren des QR-Codes. Dies sollte folgendermaßen aussehen:

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* Löschen Sie den Absatz, der Sie mit diesen Anweisungen verknüpft.

Führen Sie Ihre APP aus, und stellen Sie sicher, dass Sie den QR-Code Scannen und den Code validieren können, den der Authentifikator

## <a name="change-the-site-name-in-the-qr-code"></a>Ändern des Website namens im QR-Code

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Der Name der Website im QR-Code stammt aus dem Projektnamen, den Sie beim ersten Erstellen des Projekts auswählen. Sie können es ändern, indem Sie die- `GenerateQrCodeUri(string email, string unformattedKey)` Methode in der */Areas/- Identity /pages/Account/Manage/EnableAuthenticator.cshtml.cs* suchen.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Der Name der Website im QR-Code stammt aus dem Projektnamen, den Sie beim ersten Erstellen des Projekts auswählen. Sie können dies ändern, indem Sie die `GenerateQrCodeUri(string email, string unformattedKey)` -Methode in der Datei *pages/Account/Manage/enableauthenticator. cshtml. cs* ( Razor Pages) oder der Datei *Controllers/managecontroller. cs* (MVC) suchen.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Der Standard Code aus der Vorlage sieht wie folgt aus:

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

Der zweite Parameter im-aufrufen `string.Format` von ist Ihr Site Name, der aus dem Projektmappennamen entnommen wurde. Sie kann in einen beliebigen Wert geändert werden, muss jedoch immer URL-codiert sein.

## <a name="using-a-different-qr-code-library"></a>Verwenden einer anderen QR-Code Bibliothek

Sie können die QR-Code Bibliothek durch Ihre bevorzugte Bibliothek ersetzen. Der HTML-Code enthält ein- `qrCode` Element, in das Sie einen QR-Code mit jedem Mechanismus platzieren können, den Ihre Bibliothek bereitstellt.

Die korrekt formatierte URL für den QR-Code ist in verfügbar:

* `AuthenticatorUri` -Eigenschaft des Modells.
* `data-url` -Eigenschaft im- `qrCodeData` Element.

## <a name="totp-client-and-server-time-skew"></a>TOTP-Client-und Serverzeit Abweichung

TOTP (zeitbasierte One-Time Kenn Wort Authentifizierung) hängt vom Server-und Authentifikator-Gerät ab, das über eine genaue Zeit verfügt. Token sind nur 30 Sekunden lang. Wenn TOTP 2FA-Anmeldungen fehlschlagen, überprüfen Sie, ob die Serverzeit genau ist und vorzugsweise mit einem genauen NTP-Dienst synchronisiert wird.

::: moniker-end
