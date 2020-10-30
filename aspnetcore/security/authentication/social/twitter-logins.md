---
title: Externe Twitter-Anmelde Einrichtung mit ASP.net Core
author: rick-anderson
description: In diesem Tutorial wird die Integration der Authentifizierung von Twitter-Konto Benutzern in eine vorhandene ASP.net Core-App veranschaulicht.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: 47926d12ac5f922f2937df164d38ff6eb63cacf1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053279"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Externe Twitter-Anmelde Einrichtung mit ASP.net Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Beispiel zeigt, wie Sie es Benutzern mithilfe eines auf der [vorherigen Seite](xref:security/authentication/social/index)erstellten Beispiels ASP.net Core 3,0-Projekts ermöglichen, sich [mit Ihrem Twitter-Konto anzumelden](https://dev.twitter.com/web/sign-in/desktop-browser) .

## <a name="create-the-app-in-twitter"></a>Erstellen der app in Twitter

* Fügen Sie dem Projekt das nuget-Paket [Microsoft. aspnetcore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) hinzu.

* Navigieren Sie zu, [https://apps.twitter.com/](https://apps.twitter.com/) und melden Sie sich an. Wenn Sie noch nicht über ein Twitter-Konto verfügen, verwenden Sie den Link **[jetzt registrieren](https://twitter.com/signup)** , um einen zu erstellen.

* Wählen Sie **app erstellen** aus. Geben Sie den **APP-Namen** , die **Anwendungsbeschreibung** und den URI der öffentlichen **Website** an (Dies kann temporär sein, bis Sie den Domänen Namen registrieren):

* Aktivieren Sie das Kontrollkästchen neben **Anmelden mit Twitter aktivieren** .

* Microsoft. aspnetcore.Identity erfordert, dass Benutzer standardmäßig über eine e-Mail-Adresse verfügen. Wechseln Sie zur Registerkarte **Berechtigungen** , klicken Sie auf die Schaltfläche **Bearbeiten** , und aktivieren Sie das Kontrollkästchen neben **e-Mail-Adresse anfordern von Benutzern**

* Geben Sie Ihren Entwicklungs-URI ein, und fügen Sie ihn an `/signin-twitter` das Feld **Rückruf-URLs** an (Beispiel: `https://webapp128.azurewebsites.net/signin-twitter` ). Das Twitter-Authentifizierungsschema, das weiter unten in diesem Beispiel konfiguriert wird, verarbeitet Anforderungen bei der `/signin-twitter` Route automatisch, um den OAuth-Fluss zu implementieren.

  > [!NOTE]
  > Das URI-Segment `/signin-twitter` wird als Standard Rückruf des Twitter-Authentifizierungs Anbieters festgelegt. Sie können den Standard-Rückruf-URI ändern, während Sie die Twitter-Authentifizierungs Middleware über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [twitteroptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) -Klasse konfigurieren.

* Füllen Sie das restliche Formular aus, und klicken Sie auf **Erstellen** . Es werden neue Anwendungsdetails angezeigt:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Speichern Sie den Schlüssel und das Geheimnis der Twitter-consumerapi

Speichern Sie sensible Einstellungen wie den Schlüssel der Twitter-Consumer-API und den geheimen Schlüssel mit [Secret Manager](xref:security/app-secrets). Führen Sie für dieses Beispiel die folgenden Schritte aus:

1. Initialisieren Sie das Projekt für die geheime Speicherung gemäß den Anweisungen unter [Aktivieren der geheimen Speicherung](xref:security/app-secrets#enable-secret-storage).
1. Speichern Sie die sensiblen Einstellungen im lokalen geheimen Speicher mit den Schlüssel Schlüsseln `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Diese Token finden Sie auf der Registerkarte **Schlüssel und Zugriffs Token** , nachdem Sie eine neue Twitter-Anwendung erstellt haben:

## <a name="configure-twitter-authentication"></a>Konfigurieren der Twitter-Authentifizierung

Fügen Sie den Twitter-Dienst in der- `ConfigureServices` Methode in der Datei *Startup.cs* hinzu:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Weitere Informationen zu von der Twitter-Authentifizierung unterstützten Konfigurationsoptionen finden Sie in der [twitteroptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) -API-Referenz. Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-twitter"></a>Mit Twitter anmelden

Führen Sie die APP aus, und wählen Sie **Anmelden aus** . Eine Option zum Anmelden bei Twitter wird angezeigt:

Beim Klicken auf **Twitter** wird die Authentifizierung an Twitter umgeleitet:

Nachdem Sie Ihre Twitter-Anmelde Informationen eingegeben haben, werden Sie zurück an die Website umgeleitet, auf der Sie Ihre e-Mail festlegen können.

Sie sind jetzt mit Ihren Twitter-Anmelde Informationen angemeldet:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Problembehandlung

* **Nur ASP.net Core 2. x:** Wenn Identity nicht durch Aufrufen von `services.AddIdentity` konfiguriert `ConfigureServices` wird, führt der Versuch, sich zu authentifizieren, zu *argumumtexception: die Option "signinscheme" muss angegeben werden* . Die in diesem Beispiel verwendete Projektvorlage stellt sicher, dass dies abgeschlossen ist.
* Wenn die Standortdatenbank nicht durch Anwenden der anfänglichen Migration erstellt wurde, wird *bei der Verarbeitung des Anforderungs Fehlers ein Daten Bank Vorgang* nicht ausgeführt. Tippen Sie auf **Migrations anwenden** , um die Datenbank zu erstellen und zu aktualisieren, um den Fehler zu überschreiten.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie sich mit Twitter authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie die Website in der Azure-Web-App veröffentlicht haben, sollten Sie die `ConsumerSecret` im Twitter-Entwickler Portal zurücksetzen.

* Legen Sie die `Authentication:Twitter:ConsumerKey` und `Authentication:Twitter:ConsumerSecret` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.
